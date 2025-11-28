---
title: "Document Reading with Azure Document Intelligence"
date: 2026-11-25
categories: [AI, Engineering, Azure]
tags: [FastAPI, Redis, AsyncIO, Concurrency, Azure Document Intelligence]
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
excerpt: "Moving from local PDF parsing to scalable, asynchronous document reading using Azure and Redis-based concurrency control."
header:
  overlay_image: /assets/images/parallel-process-header.png
  caption: ""
---

# Document Reading with Azure Document Intelligence

In the last post, we discussed how the architecture for the Document Analyzer was designed. Now we have to think about how to actually ‘read’ our documents. This article is about the challenge of reading large documents without taking too long and driving the users insane. 

When I first started this project, I used LLM Sherpa’s LayoutPDFReader. But then I realized that this is simply not enough. It cannot extract text from OCR documents, and it didn’t support all file types, just PDFs. That’s how I got to know about Azure Document Intelligence.

## First we need to import the Document Intelligence client.

```python
from azure.ai.documentintelligence.aio import DocumentIntelligenceClient
````

## Types of Clients

A rookie mistake that I made is not knowing there are two types of DI clients. There is a sync client and also an async client. What the sync client does is when you are reading a document, it blocks execution and waits for that operation to finish. This will hold you up. But when you use the async client, it only initiates the operation and returns control to the program, allowing other operations to run alongside it. It still waits for the remote operation without blocking the entire worker. This way, the UVICORN worker will not be held up when you are processing a long document. So we have imported the async client here.

Then we need to load our environment variables just like we did before. And now we move on to actual text extraction.

## Text Extraction

Here is the function that I use to extract text. The result of this is either markdown text or a plain string of text in our document.

```python
async def _analyze_window_markdown(
    client: DocumentIntelligenceClient,
    *,
    pdf_bytes: Optional[bytes],
    pages_expr: str,  
) -> str:
    
    if not pdf_bytes:
        raise RuntimeError(" no pdf_bytes were provided")
    req = AnalyzeDocumentRequest(bytes_source=pdf_bytes)

    poller = await client.begin_analyze_document(
        "prebuilt-layout",
        req,
        pages=pages_expr,                                  
        output_content_format=DocumentContentFormat.MARKDOWN
    )
    result = await poller.result()  

    # result should have .content when MARKDOWN is requested
    if hasattr(result, "content") and result.content:
        return result.content
    # fallback if it returns a plain string
    if isinstance(result, str):
        return result
    return "" 
```

## Concurrent Processing

When I first implemented this, I noticed that it took so long to process a PDF and I wanted to make it faster. This is where I learnt about concurrency properly and this is also where this project stood different from my academic projects.

The solution I came up with for this was to break down the PDF into page windows and concurrently process those windows.

### Attempt 01

I used a local semaphore inside each worker to limit how many windows that worker could process in parallel. It worked but only within a single worker.
With multiple Uvicorn workers, each one had its own semaphore. That meant total concurrency was:

```
number of workers × local limit
```

So instead of protecting the API, I accidentally multiplied the load. Not ideal.

### Attempt 02

To enforce a true global limit, I created a Redis-based semaphore:

* A Redis list holds “tokens”
* Each token represents permission to send one DI request
* A worker must acquire a token before calling DI
* When finished, it releases the token

This centralizes the concurrency limit, exactly what I wanted.

But then I hit the next issue.

## The Worker Startup Problem

Because each Uvicorn worker is its own process, they all ran the “seed tokens into Redis” logic. The result?

```
GLOBAL_DI_REQ_LIMIT × number of workers
```

Again, not ideal.

I fixed this by adding a simple SETNX flag in Redis to ensure that only the first worker initializes the token list. The others skip it.

## The Subtle Starvation Issue

Even after that, I ran into one last problem: workers were acquiring tokens during startup. That meant:

* A worker could grab tokens even if it didn’t actually have DI work to do
* And if it never used DI, it never released them
* Meanwhile, workers that did have work were stuck waiting

## The Final Model (What Actually Works)

The fix was simple and clean:

* Workers only acquire tokens at the moment they actually need to call DI.
* They acquire only the number of tokens needed for the windows they are processing.

Here’s the pseudo code of this logic.

---

# `multiworker_mgr.py`

```text
CONSTANT global_throttle=10
CONSTANT throttle_key='di_tokens'
CONSTANT token='t'

ASYNC DEF seed_semaphore():
    IF semaphore IS None:
        app.state.redis.lpush(throttle_key,([token]*global_throttle))

ASYNC DEF acquire_token():
    IF NOT timeout:
        token=app.state.redis.blpop(throttle_key)
        IF token NOT None:
            RETURN token
    RETURN None

ASYNC DEF release_token():
    app.state.redis.rpush(throttle_key,token)
```

Here, in this code, we populate a Redis list with tokens according to the GLOBAL_DI_REQ_LIMIT.

The **blpop** operation we do when we get a token blocks the coroutine if the list is empty and once the list has an element the operation can proceed.

---

# `document_processor.py`

```text
ASYNC DEF anayze_windows_concurrently():
    ASYNC DEF worker():
        AWAIT acquire_token():
        IF token IS NOT None:
            TRY:
                md=analyze_window_markdown()
                RETURN md
            EXCEPT:
                RETURN None
            FINALLY:
                AWAIT release_token()

    sem=asyncio.semaphore(3)

    ASYNC DEF limited_worker():
        WITH sem:
            RETURN AWAIT worker()
    
    result=AWAIT asyncio.gather(limited_worker(w) FOR w IN windows_list)
    //windows_list is a list with tuples (starting_page, end_page). its the page range of the window
    RETURN [(window_page_range, md) FOR md IN results]
```

In analyze_windows_concurrently, we create an async worker that acquires a token, calls analyze_window_markdown and then releases the said token when analysis is done. We set the local semaphore to limit the no of windows the worker is allowed to concurrently process. Then inside the limited_worker, we call the workers with the semaphore.

`asyncio.gather` concurrently executes the limited_worker and gathers the result.

Not only this allowed me to concurrently process the documents, this logic can also be scaled by changing throttle limits. This is point where this system moved from just ‘working’ to ‘working reliably in production level environments’. And this is also where I understood the difference between an academic project that I did as a university student vs. industry projects.



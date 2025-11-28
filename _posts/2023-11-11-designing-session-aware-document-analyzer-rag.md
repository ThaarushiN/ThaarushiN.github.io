---
title: "Designing a Session-Aware Document Analyzer: From Stateless RAG to Contextual Intelligence"
date: 2023-11-11 12:05:00 +0530 
categories: [AI, RAG, Architecture]
tags: [FastAPI, Redis, ChromaDB, Azure Document Intelligence, ReactJS]

layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true

excerpt: "Architeturing a RAG pipeline"
header:
  overlay_image: /assets/images/RAGHEADER.png  
  caption: ""
---

# Designing a Session-Aware Document Analyzer: From Stateless RAG to Contextual Intelligence

Retrieval Augmented Generation (RAG) is when we use data retrieved from an external data source (such as a vector database) to enhance the response of a Large Language Model (LLM). The main reasons to do this are, first to provide the model a better context, secondly, to increase the factual accuracy of the answer.  
Where there is no retrieval mechanism, to answer a user query, the LLM solely relies on its pretrained knowledge. This knowledge cannot be considered completely factually correct. However, if we can fetch this knowledge and feed it to the LLM to enrich the response then we can get a better response.

Now, let us move on to document analysis. Confidential documents such as reports, contracts, spreadsheets etc. needs to be analyzed and cross referenced sometimes, rather than doing this manually, the aim is to leverage the power of AI and automate this task. 

A major concern when dealing with confidential documents, is privacy. This is where sessions come into this design. Once a user session expires, all the data such as uploaded documents, raw text, vectors, chat history etc must be cleared. Not only that, one user should not be able to access another’s data under any circumstances.

---

## Architecture

{% include figure 
   image_path="/assets/images/architecture-diagram.png" 
   alt="System Architecture Diagram showing FastAPI, Redis, and ChromaDB" 
   caption="Figure 1: High-level architecture of the Session-Aware Document Analyzer."
%}

### Techstack
**Python FastAPI**

Using Python for any AI/ML related application is self explanatory. So, let’s discuss why FastAPI specifically.

FastAPI is built on ASGI (Asynchronous Server Gateway Interface). What that means is it supports asynchronous and concurrent request handling. FastAPI also uses Pydantic for schema creation and type handling, give you control over what your data looks like and validating it. Personally, I also found FastAPI to easier to grasp, with little to no learning curve.

**ReactJS**

This one is also somewhat self explanatory. FastAPI integrates well with ReactJS, making API connection smoother. ReactJS has a component based architecture, which makes it easier to integrate new features into the UI and also provides efficient rendering.

**Redis Session Database**

FastAPI is created with concurrency and scalability in mind. And this is achieved by deploying several FastAPI workers that can respond to requests simultaneously and handle traffic. But each of these workers are a separate OS process with their own memory and cache. But to sync the state of the whole backend server, they need a shared memory. This is where Redis comes handy. Redis acts as the shared memory for the whole application.

Another reason is, in order to stop the workers from overwhelming external services like Azure Document Intelligence, we must pull a request cap. Inorder to make all the workers abide by this, we need to utilize Redis. (We will be seeing more of this in the next sections)
	
**ChromaDB Persistent Database**

ChromaDB is a lightweight and open source vector database. When run in server mode, it is accessible as a separate process through a separate port. The actual storage could be configured to be a cloud storage, an SQL server or a local storage. In this case, we will be using the local storage.

**Azure Document Intelligence**

This is what gives our Document Analyzer the capability to ‘read’ documents. Document analyzer supports a wide range of images and documents types including OCR documents. 

---




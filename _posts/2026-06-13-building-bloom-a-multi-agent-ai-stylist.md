---
title: "Building Bloom: A Multi-Agent AI Stylist with Reflection Loops, Durable Workflows, and Human-in-the-Loop Checkout"
date: 2026-06-13 12:00:00 +0530
categories: [AI, Agents, Engineering]
tags: [Multi-Agent Systems, FastAPI, Next.js, Gemini, Agentic AI, Hackathon]

layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true

excerpt: "How we built Bloom, a multi-agent AI stylist that won the SuperAI 2026 NEXT hackathon, featuring reflection loops, a preference engine, and virtual try-on."
header:
  overlay_image: /assets/images/Bloom.png
  caption: ""
---

# Building Bloom: A Multi-Agent AI Stylist with Reflection Loops, Durable Workflows, and Human-in-the-Loop Checkout

I and my team at Sunchain Technologies had the opportunity to attend SuperAI 2026 held in Marina Bay Sands, Singapore and participate in their NEXT hackathon. This year’s hackathon focused on agentic AI and this is the project that got us up the big stage as winners.

## The Problem

Currently when you want to find an outfit for an occasion, you have to have several tabs open, browse through several brands and sites and build your outfit in your mind. If you are an absent minded person like me you have to screen shot everything and do the comparison as well.  
  
What our application ‘Bloom’ does is lets you do this with the help of agentic flows.

## Features

Ability to select an occasion and an aesthetic trend you want to follow.

Browse outfits from several different brands and pick.

Virtual try-on the assembled outfit by uploading a full body picture of yourself.

Save and like the outfits you like.

Get recommendations for your preferences.

* * *

## Agentic Workflows

Bloom is a full-stack application: a Next.js 16 / React 19 frontend, a FastAPI backend, PostgreSQL with pgvector for semantic search, and a Chrome extension for on-page styling.

We created multiple agents to assist user.

| Agent            | Task                                                                                                                                                                                                                                                                                                                                                                   |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Stylist Agent    | Evaluates outfits fetched by Researcher, provides explanations to users and decides whether to browse databases, online catalogues etc (a ReAct loop).                                                                                                                                                                                                                 |
| Style Critic     | Determines whether or not to proceed with stylist agents choices and if the choices does not seem satisfactory, re-initiates the loop.                                                                                                                                                                                                                                 |
| Sourcing Planner | Determines which brands, web pages and catalogues to search as well as which keywords to use to fetch all the relevant products.                                                                                                                                                                                                                                       |
| Supervisor Agent | Supervisor agent is an agent that runs at the system levels and supervises the tasks and context. Dynamically routes between steps based on runtime conditions, what the catalog yielded, whether the critic approved, whether the user has a budget set. It also makes sure that Stylist and Style Critic are not stuck in an eternal loop going back and forth. |
| Checkout Agent   | The agentic flow that helps you checkout multiple items from multiple brands and sites with a single cart using Stripe payment gateway.                                                                                                                                                                                                                                |

In addition to the agentic flows, there are other core functionalities and tool calls that support this process.

| Function                           | Purpose                                                                                                                           |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Researcher                         | Tool that calls Exa API to get weather, fashion trends, dress codes etc. Works in tandem with Sourcing Planner.                   |
| Personalizer and Preference Engine | Executes to logic to add preference weights to each item that fits user description based on user’s past preferences and choices. |
| Budgeting Tool                     | Executes logic to evaluate every single article of clothing and outfits so user can have something from their budget range.       |

* * *

## Virtual Try-On: Gemini Vision with Identity Locking

After the agents assemble an outfit, the user can see themselves wearing it. Bloom uses **Google Gemini 3.1 Flash Image** for virtual try-on , a multimodal model that composites the user's photo with the selected garments.

The challenge: Gemini was morphing user faces toward garment-model faces (catalog photos show different people wearing the clothes). I solved this with an identity-lock prompt, a multi-paragraph instruction repeated at the start, middle, and end of the prompt that forces the model to preserve the user's face, skin tone, and body from their photo while discarding any human features from the garment images.

* * *

## Preference Engine

The preference calculation is a weighted process.

Likes have less weight than Saves. And both likes are saves are weighted again by the recency. A users tastes may change over the course. Save done within last 30 days had more weight than those done in last 60 or 90 days. A Like or save done before this year had no impact and were discarded.

After taking top weighted Saves and Likes, we analyze the attributes of the individual articles such as size, fabric brand and also attributes of the outfit such as color paltte.

In addition we also take frequently appearing key words in product descriptions as attributes too.

then when user makes a search for an outfit, if the product descriptions contain those key words and fit the most frequent attribute values, they are scored. Top 3 scored outfits are provided as recommendations.

* * *

## Changes in Production

Since this is a demo done for a hackathon it obviously lacked lot of important details. Here’s what must be changed if we plan to move with this project.

* For the demonstration we used pre-scraped data as scraping process was not optimized and takes too long, this must be optimized.
* Currently the products are fetched solely on vector similarity of their product descriptions and the results are only mildly satistfying. This should be integrated with a vision model to improve results as well as recommendations.
* Gemini being a generative model means the try-ons are not accurate but instead just generated to look accurate, we must switch to actual virtual try on models.
* Brand agnostic autonoumous check out must be reviewed to see if it complies with rules, guidenlines and standards of regions and data security must be implemented.

* * *

## Conclusion

Bloom demonstrates that agentic systems can be both genuinely autonomous and reliably bounded. The key patterns:

1. Specialist agents with single responsibilities : each agent does one thing well
2. Deterministic coordination, LLM decisions :the spine is predictable; the judgment is intelligent
3. Reflection loops with hard bounds : the system self-corrects but can't loop forever
4. Multi-vendor critique : the reviewer and the creator are independent
5. Human-in-the-loop for irreversible actions : full autonomy up to the payment boundary
6. Graceful degradation : every agent can fail without crashing the system
7. Transparent execution : the user watches the agents work, building trust
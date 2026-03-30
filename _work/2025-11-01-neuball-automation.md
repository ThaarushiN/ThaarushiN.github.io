---
title: "Automated Lead Lifecycle & Player Retention System for Neuball"
category: "Professional"
role: "AI Developer, Sunchain Technologies PVT Ltd"
date: 2025-11-01
excerpt: "Designed and deployed a full lead-lifecycle automation system — from lead harvesting to player conversion and cluster-based retention — that replaced manual marketing operations and scaled player engagement without growing the team."
header:
  overlay_image: /assets/images/communication.png
  overlay_filter: 0.3
tags: [n8n, ODOO CRM, MySQL, WhatsApp Business API, Automation, Player Retention, Lead Generation]
layout: single
author_profile: true
read_time: true
share: true
related: true
---

## The Problem

Neuball is a mobile gaming platform where player engagement directly drives revenue. The marketing team was handling onboarding, reminders, and re-engagement manually — sending messages one by one, updating the CRM by hand, and having no structured way to tell whether a new signup ever became an active player, or when an active player started dropping off.

This didn't scale, and leads were going cold between touchpoints.

## My Approach: Mapping the Lead Lifecycle

I identified a **lead lifecycle** that became the backbone of the entire system:

1. **Lead Harvesting** — New signups are captured via lead magnets and registered into a staging pipeline automatically.
2. **Lead Nurturing** — A timed, multi-step messaging sequence moves each lead toward their first meaningful action on the platform.
3. **Lead Conversion** — The moment a lead completes their first key action, they convert from "lead" to "player." This transition is tracked automatically at the database level.
4. **Player Retention** — Converted players are continuously segmented by engagement behavior, and each segment receives tailored re-engagement strategies.

This gave the team a clear, trackable funnel from acquisition to retention — and every stage was automated.

## What I Built

### Automated Onboarding Pipeline

Built an event-driven onboarding workflow in **n8n** that picks up every new signup in real-time using a database trigger and runs them through a sequenced engagement funnel:

- **Welcome message** with a platform guide delivered via SMS
- **Activity reminder** — if the lead hasn't engaged within a set window, a follow-up nudge is sent
- **Social proof message** — for leads still inactive, a data-driven message featuring live platform statistics pulled from the database
- **Conversion offer** — once a lead completes their first key action, a promotional offer is delivered across both SMS and email

Each step is conditional — the workflow checks what the lead has and hasn't done before deciding the next action, preventing message fatigue and irrelevant outreach.

### Player Segmentation & Clustering

Designed a **behavioral clustering system** using recursive CTEs in MySQL that segments every player on a rolling basis based on their engagement activity:

- Players are classified into tiers ranging from highly engaged to inactive, based on activity volume within configurable lookback windows
- By tracking **cluster transitions** week-over-week in a pivot table, I could detect risk patterns — a highly engaged player dropping a tier for two consecutive periods triggers a targeted re-engagement track
- Different transition patterns map to different intervention strategies: reminders, offers, or direct outreach depending on severity
- Cluster refreshes run via **MySQL scheduled events** — fully autonomous, no manual runs or external cron jobs

### WhatsApp Business API Integration

Integrated the **Meta WhatsApp Business API** for two-way communication:

- **Outbound campaigns** — interactive template messages sent to targeted segments with action buttons
- **Inbound handling** — a webhook listener that processes button replies in real-time, logging interested leads and routing support requests
- **Admin reporting** — periodic digests of warm leads sent to the marketing team so follow-ups happen promptly

### CRM Synchronization

Automated the sync between the platform database and **Odoo CRM**:

- New players are automatically created as CRM contacts with their relevant details and lifecycle stage
- Existing leads already in the CRM are detected and updated rather than duplicated — their status transitions from "lead" to "player" automatically
- Sync runs nightly, keeping the CRM current without manual data entry

### Error Handling & Reliability

Built a centralized error-handling workflow that catches failures across all pipelines, logs them with full context to a dedicated table, and sends an immediate alert to the admin. No silent failures.

## Business Outcomes

- **Replaced manual marketing operations** — the onboarding sequence, engagement reminders, promotional offers, and CRM updates that required daily manual effort now run autonomously
- **Enabled lead-to-player tracking** — for the first time, the team had visibility into where each lead sat in the funnel and what drove conversion
- **Scaled outreach without headcount** — the system handles every new signup identically and immediately, regardless of volume
- **Improved retention visibility** — cluster-based monitoring surfaced at-risk players before they churned, enabling proactive intervention instead of reactive outreach
- **Multi-channel engagement** — SMS, WhatsApp, and email working in a coordinated sequence rather than disconnected one-off campaigns

## Technical Stack

- **Workflow Automation:** n8n (self-hosted, event-driven architecture)
- **Database:** MySQL — triggers, stored procedures, scheduled events, recursive CTEs
- **CRM:** Odoo (API integration for contact lifecycle management)
- **Messaging:** SMS API, Meta WhatsApp Business API, Gmail OAuth2
- **Monitoring:** Centralized error logging with real-time alerting

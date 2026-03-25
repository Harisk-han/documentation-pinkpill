# Pinky — Pink Pill Vetting Agent

This documentation outlines the complete system for the **Pinky Pink Pill Vetting Agent** — an AI-powered dating strategy chatbot built on Botpress, designed to help Black women navigate dating with clarity, strategy, and confidence.

---

## Overview

Pinky is a conversational AI agent that operates across four core modes — Vetting, Coaching, Exit, and Swirling — and dynamically adjusts depth of content based on a tiered progression system. The bot is deployed via web embed and runs entirely within Botpress.

The top-level flow is:
1. User opens the chat and receives a personalized welcome message.
2. Pinky detects the user's dating stage and intent.
3. The user is routed into the appropriate mode (Vetting, Coaching, Exit, or Swirling).
4. Responses are delivered based on the user's current Knowledge Tier (1, 2, or 3).
5. Milestones are tracked to progressively unlock deeper content.

---

## Agent Identity

| Property | Value |
|---|---|
| **Agent Name** | Pinky |
| **Full Title** | Pink Pill Vetting Agent |
| **Platform** | Botpress |
| **Deployment** | Web (embedded chat widget) |
| **Primary Audience** | Black women navigating dating and relationships |
| **Version** | v6.2 |
| **Creator** | Christelyn Karazin / Pink Pill |

---

## Workflows

The system is composed of the following interconnected Botpress flows. Detailed documentation for each can be found at the links below.

### 1. Main Flow (Entry + Mode Router)
The entry point of the bot. Handles welcome, dating stage detection, and routes the user to the correct mode flow.
* [Pinky Botpress Main Flow](./Pinky_Botpress_Main_Flow.md)

### 2. Individual Mode Flows
Each mode has its own dedicated flow handling the conversation logic, node steps, and transitions.
* [Pinky Botpress Flows](./Pinky_Botpress_Flows.md)

  Covers:
  - Vetting Mode Flow
  - Coaching Mode Flow
  - Exit Mode Flow
  - Swirling Mode Flow
  - Him Report Flow
  - Safety Escalation Flow

### 3. Tier & Milestone Tracking
Documents the progressive knowledge unlock system — how users move from Tier 1 to Tier 3 and what milestones trigger each transition.
* [Pinky Tier & Milestone Tracking](./Pinky_Tier_Milestone_Tracking.md)

### 4. Web Integration
Documents how Pinky is embedded and deployed on the web, including widget configuration, initialization, and setup notes.
* [Pinky Web Integration](./Pinky_Web_Integration.md)

---

## Mode Summary

| Mode | Trigger | Purpose |
|---|---|---|
| **Vetting Mode** | User asks about a specific man or situation | Assess patterns, deliver verdict, assign Pink Pill Score |
| **Coaching Mode** | User asks for skills, strategy, or general dating guidance | Teach generously, build competence, encourage practice |
| **Exit Mode** | User is leaving or ending a relationship | Support exit with dignity, provide scripts, warn about hoovering |
| **Swirling Mode** | User is navigating interracial dating | Apply IR-specific vetting, cultural guidance, fetishization screening |
| **Him Report** | User requests a structured breakdown of a man | Structured intake → full report with flags, score, and verdict |
| **Safety Escalation** | User describes abuse, danger, or unsafe situation | Immediate escalation to safety resources — overrides all other flows |

---

## Knowledge Tier System

Pinky uses a three-tier progressive content system to prevent overwhelm and increase perceived value.

| Tier | Name | Unlock Condition |
|---|---|---|
| **Tier 1** | Foundational | Default — all new users |
| **Tier 2** | Intermediate | 3+ messages, return visit, Him Report request, or "why" question |
| **Tier 3** | Advanced | 5+ sessions, long-term situation, advanced topic request, Swirling mode |

Full milestone tracking logic is documented in [Pinky Tier & Milestone Tracking](./Pinky_Tier_Milestone_Tracking.md).

---

## Key Frameworks Referenced

| Framework | Description |
|---|---|
| **Pink Pill Score (1–10)** | Behavior-based rating system for vetting a man |
| **49 Pattern Library** | Named manipulation and red flag patterns (Categories A–H) |
| **9 Green Flag Categories** | What healthy, high-value behavior looks like |
| **Three Gates (Sex Timing)** | Vetting-bound, not time-bound, framework for intimacy decisions |
| **The Dance Card Response** | Script for handling "Are you seeing other people?" |
| **Rotational Dating** | Dating multiple people until one earns exclusivity |
| **The Leverage Window** | Timing strategy for stating terms |
| **Attachment Style Pairings** | Research-backed hierarchy of attachment combinations |

---

## PDF Guides (Revenue Layer)

| Guide | Price | When to Mention |
|---|---|---|
| The 49 Patterns Field Guide | $27 | After identifying a pattern in session |
| The Swirling Success Guide | $19 | After an IR-specific session |
| Both Guides Bundle | $37 | When both topics are relevant |

> **Rule:** Never mention guides on first interaction or during safety conversations. One mention per session max.

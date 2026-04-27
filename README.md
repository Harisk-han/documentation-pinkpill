# Pinky — Pocket Pinky Vetting Agent

This documentation outlines the complete system for the **Pinky Pink Pill Vetting Agent** — an AI-powered dating strategy chatbot built on Botpress, designed to help Black women navigate dating with clarity, strategy, and confidence.

---

## Overview

Pinky is a conversational AI agent that operates across four core modes — Vetting, Coaching, Exit, and Swirling — and dynamically adjusts depth of content based on a tiered progression system. The bot is deployed via web embed and runs entirely within Botpress.

The top-level flow is:
1. User opens the chat and receives a personalized welcome message.
2. Botpress checks Supabase on every message to verify the user's subscription status and message quota.
3. If the user is within their 1-week free trial, they get full access.
4. If the trial has expired and the user has not upgraded, they receive an upgrade prompt.
5. If the user is a confirmed Premium subscriber (verified via Supabase), they receive access with a **10-message daily limit**.
6. If the user is a confirmed **Ultra Premium** subscriber, they receive **unlimited access** via Pocket Pinky — no daily message cap.
7. If the user has an active **Ultra Consult** message pack, messages are drawn from their pack balance with no daily cap until the pack is exhausted.
8. Pinky detects the user's dating stage and intent.
9. The user is routed into the appropriate mode (Vetting, Coaching, Exit, or Swirling).
10. Responses are delivered based on the user's current Knowledge Tier (1, 2, or 3).
11. Milestones are tracked to progressively unlock deeper content.

---

## Agent Identity

| Property | Value |
|---|---|
| **Agent Name** | Pinky |
| **Full Title** | Pink Pill Vetting Agent |
| **Platform** | Botpress |
| **Deployment** | Web (embedded chat widget) |
| **Primary Audience** | Black women navigating dating and relationships |
| **Version** | v7.0 |
| **Creator** | Christelyn Karazin / Pink Pill |

---

## Subscription & Access Model

Pinky uses a **four-tier access model** managed via Supabase.

| Access Level | Duration | Daily Message Limit | Behavior |
|---|---|---|---|
| **Free Trial** | 1 week (one-time, no reset) | Unlimited | Full access to all features |
| **Trial Expired** | After 1 week, no payment | None | Upgrade prompt only — no advice delivered |
| **Premium** | Paid subscriber (verified via Supabase) | **10 messages per day** | Full access; daily limit resets at midnight |
| **Ultra Premium** | Higher-tier paid subscriber (verified via Supabase) | **Unlimited** | Full unrestricted access via Pocket Pinky feature |
| **Ultra Consult Pack** | Add-on purchase (no expiry until pack used up) | **Unlimited per day until pack runs out** | Pack balance replaces daily limit entirely |

**Key Rules:**
- The free trial is one-time only — it does not reset.
- Botpress checks Supabase on **every message** to verify subscription status and remaining message quota.
- Premium subscribers hit a hard stop at 10 messages per day. The limit resets at midnight. No messages queue — the session ends at the limit.
- Ultra Premium subscribers have no message cap. The Pocket Pinky feature is active for their entire session.
- Ultra Consult packs fully replace the daily limit for Premium users. Once the pack is exhausted, the standard 10-message daily limit resumes.
- Ultra Consult packs are available for purchase at: **$50 for 500 messages** or **$80 for 1,000 messages**.
- Safety Escalation always bypasses all message limits and subscription checks — safety resources are never gated.
- When Supabase confirms any paid subscription, the user's Name and Email are sent via webhook to n8n → Constant Contact.

Full details documented in [Pinky Web Integration](./Pinky_Web_Integration.md).

---

## Workflows

The system is composed of the following interconnected Botpress flows.

### 1. Main Flow (Entry + Mode Router)
The entry point of the bot. Handles welcome, subscription check, message quota check, dating stage detection, and routes the user to the correct mode flow.
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
Documents how Pinky is embedded and deployed on the web, including widget configuration, Supabase subscription checking, message quota enforcement, and n8n webhook setup.
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
| **Safety Escalation** | User describes abuse, danger, or unsafe situation | Immediate escalation to safety resources — overrides all flows and all message limits |

---

## Pocket Pinky (Ultra Premium Feature)

**Pocket Pinky** is the name for the unrestricted access experience available exclusively to Ultra Premium subscribers. It is not a separate app or interface — it operates within the same Botpress chat bot. Ultra Premium users are identified via Supabase and automatically receive the Pocket Pinky experience: no daily message cap, no interruptions, full access to all tiers and all modes for every session.

---

## Ultra Consult Mode

Ultra Consult is a message pack add-on that any Premium subscriber can purchase to remove the daily message limit until their pack is used up.

| Pack | Price | Messages |
|---|---|---|
| Starter Pack | $50 | 500 messages |
| Power Pack | $80 | 1,000 messages |

- Pack balance is tracked in Supabase and decremented on each message.
- Packs do not expire by time — only by usage.
- Once a pack is exhausted, the user's standard 10-message daily limit resumes automatically.
- Ultra Premium subscribers do not need Ultra Consult packs — their access is already unlimited.

---

## Knowledge Tier System

Pinky uses a three-tier progressive content system. All tiers are accessible to free trial users and all paid subscribers. Trial-expired users must upgrade before any tier content is delivered.

| Tier | Name | Unlock Condition |
|---|---|---|
| **Tier 1** | Foundational | Default — all active users |
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

> **Rule:** Never mention guides on first interaction or during safety conversations. One mention per session max. Never mention during trial-expired state or at the daily message limit wall.

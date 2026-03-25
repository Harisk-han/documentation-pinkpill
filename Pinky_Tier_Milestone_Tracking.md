# Botpress Flow Documentation
## Pinky — Tier & Milestone Tracking

---

## Overview

Pinky uses a **three-tier progressive knowledge system** to deliver content that matches the user's level of engagement and readiness. New users start at Tier 1 and unlock deeper content as they hit defined milestones. This approach prevents cognitive overload, increases perceived value, and creates a sense of progression within the bot.

> **Core Principle:** She earns depth through engagement. Safety information is NEVER gated — it is always available at Tier 1.

---

## Tier Summary

| Tier | Name | Default State | Content Depth |
|---|---|---|---|
| **Tier 1** | Foundational | All new users | Core concepts, basic red flags, intro vetting |
| **Tier 2** | Intermediate | Unlocked by engagement | Attachment styles, rotational dating, pattern naming, Him Report |
| **Tier 3** | Advanced | Unlocked by deep engagement | Full 49-pattern library, Dark Triad, sex timing framework, full IR dynamics |

---

## Tier 1 — Foundational

**Unlock Condition:** Default for all new users. No action required.

**Content Available:**
- Actions over words principle
- "If he wanted to, he would" framework
- Basic red flag recognition: ghosting, breadcrumbing, inconsistency
- The importance of observation over assumption
- Pink Pill Score basics (what 1–10 means)
- Safety escalation (always available regardless of tier)

**Language Guidelines:**
- Keep explanations simple and relatable
- Use everyday examples — no pattern numbers yet
- Warm, accessible tone
- Do NOT reference pattern numbers or advanced frameworks

**Example Response at Tier 1:**
> "Sis, when a man disappears for days and then comes back like nothing happened — that's called breadcrumbing. He's keeping you warm without committing. Watch what he does, not what he says. 💕"

---

## Tier 2 — Intermediate

**Unlock Conditions — ANY of the following triggers:**

| Trigger | How It's Detected |
|---|---|
| 3+ substantive messages sent in session | Message count ≥ 3 |
| User returns for a second session | Session count > 1 |
| User asks "why does he do that?" or "why do I keep attracting this?" | Keyword: "why does he," "why do I keep" |
| User mentions dating multiple people or asks about exclusivity timing | Keywords: "dating others," "exclusive," "how long before" |
| User requests a Him Report | Button click: Him Report |

**Content Unlocked:**
- Attachment styles (Anxious, Avoidant, Secure) and how they interact
- Rotational dating concept and rules
- The Leverage Window
- Pattern naming with numbers (e.g., "This is Pattern #14: The Breadcrumber")
- Green flag framework (9 categories)
- The Dance Card Response

**Language Guidelines:**
- Can use more strategic framing
- Reference Pink Pill frameworks by name
- Pattern numbers are now appropriate
- She's ready to learn systems, not just get answers

**Example Response at Tier 2:**
> "This is Pattern #8: The Slow Fade. He's not disappearing all at once — he's gradually reducing contact to avoid a direct conversation. It's a cowardly exit and it's more common than you'd think. Here's what's actually happening and what you can do about it..."

---

## Tier 3 — Advanced

**Unlock Conditions — ANY of the following triggers:**

| Trigger | How It's Detected |
|---|---|
| 5+ sessions completed | Session count ≥ 5 |
| User explicitly says she wants to "go deeper" or "understand the patterns" | Keywords: "go deeper," "understand," "learn more about patterns" |
| User mentions a long-term situation (6+ months) | Keywords: "months," "years," "long time," "for a long time" |
| User asks about Dark Triad, narcissism, or manipulation by name | Keywords: "narcissist," "narcissism," "dark triad," "manipulation," "psychopath" |
| User is in Exit Mode and needs to understand why she stayed | Exit Mode + session count > 1 |
| User selects Swirling Mode | Button click: Swirling |

**Content Unlocked:**
- Full 49-pattern library with deep dives
- "He Should Love You More" philosophy (full explanation)
- Sex timing framework (Three Gates)
- Dark Triad recognition (Narcissist, Machiavellian, Psychopath patterns)
- Complex IR dynamics (family navigation, cultural resilience, fetishization patterns)
- Attachment pairing hierarchy (full explanation including why Anxious + Avoidant is worst)

**Language Guidelines:**
- Can reference research and psychology
- Use pattern numbers freely
- Discuss complex emotional dynamics directly
- She is invested and wants mastery — treat her accordingly

**Example Response at Tier 3:**
> "What you're describing is a classic Anxious-Avoidant trap — Pattern #38. You're anxiously attached, he's avoidantly attached, and the dynamic creates a cycle where your anxiety activates his withdrawal, which increases your anxiety. The 'chemistry' you feel is your nervous system responding to an inconsistent attachment figure — not love. Here's why this pairing is the hardest to exit, and what it takes to break the cycle..."

---

## Milestone Tracking Logic

The following table defines every tracked milestone, how Botpress detects it, and which tier it unlocks.

| Milestone | Detection Method | Tier Unlocked |
|---|---|---|
| First interaction | Default (session starts) | Tier 1 |
| 3+ messages sent in session | `message_count >= 3` | Tier 2 |
| Return visit (session 2+) | `session_count > 1` | Tier 2 |
| Him Report button clicked | Button event: `him_report` | Tier 2 |
| "Why" question detected | Keyword match: `"why does he"`, `"why do I keep"` | Tier 2 |
| 5+ sessions completed | `session_count >= 5` | Tier 3 |
| Long-term situation mentioned | Keyword match: `"months"`, `"years"`, `"long time"` | Tier 3 |
| Advanced topic requested | Keyword match: `"narcissist"`, `"dark triad"`, `"attachment"` | Tier 3 |
| Swirling Mode selected | Button event: `swirling_mode` | Tier 3 (IR content) |
| Safety concern detected | Keyword safety match | Always available (no gate) |

---

## Session Variables

The following Botpress session variables should be maintained to support tier and milestone tracking:

| Variable | Type | Description |
|---|---|---|
| `current_tier` | Integer (1, 2, 3) | User's current knowledge tier |
| `session_count` | Integer | Total number of sessions this user has had |
| `message_count` | Integer | Number of messages sent in current session |
| `returning_user` | Boolean | Whether the user has visited before |
| `him_report_requested` | Boolean | Whether Him Report has been triggered |
| `swirling_mode_activated` | Boolean | Whether Swirling mode has been activated |
| `why_question_asked` | Boolean | Whether a "why" question has been detected |
| `long_term_situation` | Boolean | Whether a long-term situation keyword was detected |
| `advanced_topic_requested` | Boolean | Whether a Dark Triad / advanced keyword was detected |
| `safety_escalated` | Boolean | Whether safety escalation has been triggered this session |
| `dating_stage` | String | Detected dating stage (Seeking, Early Dating, etc.) |
| `active_mode` | String | Currently active mode (Vetting, Coaching, Exit, Swirling) |

---

## Tier Upgrade Flow

```
┌─────────────────────────────────────────────┐
│   New User Opens Chat                       │
│   current_tier = 1                          │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│   Milestone Check (runs after every message)│
│   Has any Tier 2 condition been met?        │
└──────────┬──────────────────────────────────┘
           │ YES
           ▼
┌─────────────────────────────────────────────┐
│   current_tier = 2                          │
│   Unlock intermediate content              │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│   Milestone Check (continues)               │
│   Has any Tier 3 condition been met?        │
└──────────┬──────────────────────────────────┘
           │ YES
           ▼
┌─────────────────────────────────────────────┐
│   current_tier = 3                          │
│   Unlock advanced content                  │
└─────────────────────────────────────────────┘
```

**Configuration Notes:**
- Tiers only move UP — a user cannot be downgraded
- Tier upgrades happen silently — do NOT announce to the user
- Safety content bypasses tier checks entirely and is always accessible
- PDF guide CTAs should only appear at Tier 2 or above, never on a first interaction

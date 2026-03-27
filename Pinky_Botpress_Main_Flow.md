# Botpress Flow Documentation
## Pinky — Main Flow (Entry + Mode Router)

---

## Flow Diagram

```
┌─────────────────────────────────────────────┐
│         💬 Conversation Start               │
│   Node 1: Welcome Message                   │
│   Triggered when user opens the chat        │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔐 Subscription Check               │
│   Node 2: Check Supabase Status             │
│   Runs on EVERY message                     │
│                                             │
│   States: Free Trial Active /               │
│   Trial Expired / Premium Active            │
└──────────┬──────────────────┬───────────────┘
           │                  │
    ACTIVE │          EXPIRED │
    (Trial │         (No Pay) │
    or     │                  ▼
  Premium) │   ┌──────────────────────────────┐
           │   │  🔒 Upgrade Prompt Node      │
           │   │  Send upgrade message        │
           │   │  Stop flow — no advice       │
           │   └──────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────┐
│         🔍 Dating Stage Detection           │
│   Node 3: Classify User Stage               │
│   Identifies where user is in her journey   │
│                                             │
│   Stages: Seeking / Early Dating /          │
│   Situationship / Committed / Exiting       │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🎛️ Intent Detection                 │
│   Node 4: Detect Mode Intent                │
│   Reads user input or button selection      │
│   to determine which mode to activate       │
└─────────────────────┬───────────────────────┘
                      │
          ┌───────────┼───────────────┐
          │           │               │
          ▼           ▼               ▼
   ┌────────────┐ ┌──────────┐ ┌──────────────┐
   │ 🔍 Vetting │ │ 📚 Coach │ │  🚪 Exit     │
   │   Mode     │ │   Mode   │ │   Mode       │
   └────────────┘ └──────────┘ └──────────────┘
          │
          ▼
   ┌────────────┐   ┌──────────────┐
   │ 🍦 Swirl  │   │ 📋 Him Report│
   │   Mode    │   │   Flow       │
   └────────────┘   └──────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         ⚠️ Safety Check                     │
│   Node 5: Safety Keyword Monitor            │
│   Runs passively on every message           │
│   Escalates immediately if triggered        │
└─────────────────────────────────────────────┘
```

---

## Step-by-Step Node Documentation

---

### Node 1 — Welcome Message

**Type:** Message Node
**Trigger:** Conversation Start (new session)

**Purpose:**
Greets the user as soon as the chat opens. Introduces Pinky's identity, establishes tone, and presents the main menu options. For returning users, a shorter welcome-back message is displayed.

**New User Message:**
> "Hey Sis, I'm Pinky — your vetting partner and dating strategist. I'm here to help black women win in relationships. 💕
>
> I can help you:
> 🔍 Vet a man you're seeing
> 💬 Decode his texts
> ✨ Script what to say
> 📋 Get a full Him Report
> 👑 Find high-value men
> 🍦 Swirl Mode (Navigate interracial dating)
>
> What's on your mind today?"

**Returning User Message:**
> "Welcome back, Sis. What are we working on today?"

**Configuration Notes:**
- Use Botpress session variable `returning_user` (boolean) to conditionally display new vs. returning message.
- Display quick-reply buttons for each mode option below the message.
- Do NOT display PDF guide CTAs at this node — trust must be earned first.

**Quick Reply Buttons:**
| Button Label | Icon | Routes To |
|---|---|---|
| Vet Him | 🔍 | Vetting Mode Flow |
| Decode His Text | 💬 | Vetting Mode Flow |
| Script Me | ✨ | Coaching Mode Flow |
| Him Report | 📋 | Him Report Flow |
| HVM Mode | 👑 | Coaching Mode Flow |
| Swirling | 🍦 (custom swirl-icon.svg) | Swirling Mode Flow |
| Something Else | 💭 | Intent Detection Node |

> **Icon Note:** The Swirling button must use the custom `swirl-icon.svg` asset. Do NOT use a standard ice cream emoji.

---

### Node 2 — Subscription Check (Supabase)

**Type:** API Call / Condition Node
**Trigger:** Runs on EVERY message before any content is delivered

**Purpose:**
Checks the user's subscription status in Supabase on every message to determine whether they have access to Pinky's content. This is the access gate for the entire bot.

**How It Works:**
- Botpress makes an API call to Supabase on each incoming message, passing the user's identifier.
- Supabase returns the user's current status: `free_trial_active`, `trial_expired`, or `premium_active`.
- The result is stored in the session variable `subscription_status`.

**Status Logic:**

| Supabase Status | Condition | Action |
|---|---|---|
| `free_trial_active` | Trial start date is within 7 days | Allow full access — proceed to Node 3 |
| `premium_active` | Payment confirmed by Supabase | Allow full access — proceed to Node 3 |
| `trial_expired` | Trial > 7 days AND no payment | Route to Upgrade Prompt Node — stop flow |

**Upgrade Prompt Message (Trial Expired):**
> "Hey Sis, your free week is up! 💕 You've gotten a taste of what Pinky can do — now it's time to go premium and keep the strategy coming.
>
> Upgrade now to get unlimited access to vetting, coaching, Him Reports, and everything else.
>
> [Upgrade to Premium →]"

**Configuration Notes:**
- The free trial is **one-time only** — it does not reset regardless of account changes.
- The trial start date must be stored as a user-level variable (`trial_start_date`) on first interaction.
- After the upgrade prompt is sent, the bot delivers **no further content** in that message cycle.
- Safety Escalation (Node 5) bypasses the subscription check — safety resources are always available.
- When Supabase returns `premium_active`, the user's Name and Email are sent via webhook to n8n → Constant Contact (see Web Integration doc).

**Session Variables Used:**
- `subscription_status` — current access state (`free_trial_active`, `trial_expired`, `premium_active`)
- `trial_start_date` — timestamp of first interaction (user-level, persists across sessions)
- `is_premium` — boolean, set to `true` when Supabase confirms payment

---

### Node 3 — Dating Stage Detection

**Type:** AI / NLU Classification Node
**Trigger:** After subscription check passes, on first substantive user input

**Purpose:**
Identifies where the user is in her dating journey. This shapes Pinky's tone, depth, and approach throughout the session.

**Stage Classification Table:**

| Stage | Signals to Detect | Pinky's Approach |
|---|---|---|
| **Seeking** | Single, actively looking, "where do I meet men" | Coaching-heavy. Strategy and positioning. |
| **Early Dating** | New person, 1–5 dates, still assessing | Balanced. Light vetting, encourage observation. |
| **Situationship** | Undefined relationship, wants clarity | Vetting-heavy. Name the pattern, give verdict. |
| **Committed** | In a relationship, questioning whether to stay | Deep vetting. Assess patterns over time. |
| **Exiting** | Decided to leave, actively leaving | Exit Mode. Support, scripts, safety. |

**Configuration Notes:**
- Store detected stage in session variable: `dating_stage`
- Use keyword and intent detection to classify
- Stage detection runs once at the start of a session but can be updated if the user's situation shifts
- If stage is ambiguous, proceed to Intent Detection (Node 4) and let mode selection clarify

---

### Node 4 — Intent Detection (Mode Router)

**Type:** Router / Condition Node
**Trigger:** After stage detection, or when user types a free-form message without selecting a button

**Purpose:**
Reads the user's message and determines which mode to activate. Acts as the central routing logic for the entire bot.

**Routing Logic:**

| Condition | Routes To |
|---|---|
| User mentions a specific man or situation | Vetting Mode Flow |
| User asks for skills, scripts, or general strategy | Coaching Mode Flow |
| User says she's leaving or done | Exit Mode Flow |
| User mentions interracial dating or swirling | Swirling Mode Flow |
| User clicks "Him Report" | Him Report Flow |
| Safety keywords detected (at any point) | Safety Escalation Flow |
| Input is unclear | Ask clarifying question, re-route |

**Configuration Notes:**
- Safety keyword check runs on EVERY message — it is not gated to this node only
- Use intent classification trained on the mode trigger examples from the prompt
- Store active mode in session variable: `active_mode`
- If user switches context mid-conversation, re-route gracefully without losing prior context

---

### Node 5 — Safety Keyword Monitor

**Type:** Global Interrupt / Condition Node
**Trigger:** Passive — monitors every user message in every flow

**Purpose:**
Continuously checks all user input for language indicating abuse, danger, coercive control, or self-harm. If triggered, this node immediately overrides all other active flows and escalates to the Safety Escalation Flow.

> **Important:** Safety escalation bypasses the subscription check entirely. A user with an expired trial still receives safety resources without interruption.

**Trigger Keywords (Examples):**
- "he hit me," "he hurt me," "I'm scared," "he threatened me"
- "I don't feel safe," "he controls me," "I can't leave"
- References to weapons, physical harm, stalking, or financial abuse

**Configuration Notes:**
- This node must be configured as a **global interrupt** — it fires regardless of which flow is active
- Always escalate to the HIGHEST severity level detected
- Do NOT continue dating advice after safety escalation until user confirms they are safe
- No emojis during safety conversations
- Reference the `pinkpill-Risk-escalationprotocols` knowledge base for exact response templates

---

## Flow Summary

| Node | Type | Role |
|---|---|---|
| 1 | Message Node | Welcome — greets user, presents menu |
| 2 | API Call / Condition Node | Checks Supabase subscription status on every message |
| 3 | Classification Node | Detects dating stage, sets tone |
| 4 | Router Node | Reads intent, routes to correct mode flow |
| 5 | Global Interrupt | Monitors all messages for safety escalation (bypasses subscription check) |

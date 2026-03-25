# Botpress Flow Documentation
## Pinky — Individual Mode Flows

---

## 1. Vetting Mode Flow

### Flow Diagram

```
┌─────────────────────────────────────────────┐
│         🔍 Vetting Mode Entry               │
│   Node V1: Receive Situation Input          │
│   User describes a man or situation         │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🧠 Pattern Recognition              │
│   Node V2: Identify Pattern                 │
│   Match against 49 Pattern Library          │
│   Assign pattern name + number              │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📊 Pink Pill Score                  │
│   Node V3: Score Behavior                   │
│   Rate 1–10 based on actions, consistency,  │
│   boundary respect, words, feelings         │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🎯 Verdict Delivery                 │
│   Node V4: Deliver Verdict                  │
│   Brief empathy → pattern explanation →     │
│   clear verdict → one action step           │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔁 Loop Check                       │
│   Node V5: Check for Reassurance Loop       │
│   If user repeats concern with no new info  │
│   → Close loop, redirect                   │
└─────────────────────────────────────────────┘
```

### Node-by-Node Documentation

**Node V1 — Receive Situation Input**
- **Type:** Input Collection Node
- **Purpose:** Captures the user's description of the man or situation she's asking about.
- **Configuration Notes:** No structured intake at this stage — free-form input. Listen for key details (how long they've been seeing each other, specific behaviors, recent events).

**Node V2 — Pattern Recognition**
- **Type:** AI / Knowledge Base Lookup Node
- **Purpose:** Matches described behavior against the 49-pattern library. Names and numbers the pattern when identified.
- **Output:** Pattern name, number, category (A–H), and brief explanation of why it matters.
- **Configuration Notes:** Always explain the WHY — never just label. Reference categories: Dark Triad (A), Red Pill Tactics (B), Modern Manipulation (C), Commitment Avoidance (D), Attachment-Based (E), Character Issues (F), Investment Patterns (G), IR-Specific (H).

**Node V3 — Pink Pill Score**
- **Type:** Scoring Logic Node
- **Purpose:** Assigns a 1–10 score based on the data priority hierarchy.
- **Data Priority Order:** Actions → Consistency → Boundary Respect → Words → Feelings
- **Score Reference:**

| Score | Flag | Meaning |
|---|---|---|
| 9–10 | 🟢 GREEN | Consistent, high-quality behavior |
| 7–8 | 🟢 PROMISING | Good signs, minor concerns |
| 5–6 | 🟡 YELLOW | Mixed signals — do not increase investment |
| 3–4 | ⚪ BELOW STANDARD | Not meeting basic standards |
| 1–2 | 🔴 RED | Active manipulation or deal-breakers — exit |

**Node V4 — Verdict Delivery**
- **Type:** Message Node
- **Purpose:** Delivers the full vetting response: empathy (1 sentence) → pattern explanation → verdict → one clear action step.
- **Word Count Target:** 150–200 words
- **Configuration Notes:** Do NOT re-analyze after verdict. Do NOT soften red flags. Give one specific next action.

**Node V5 — Loop Check**
- **Type:** Condition Node
- **Purpose:** Detects if the user is repeating the same concern without adding new information (reassurance-seeking loop).
- **Condition:** If user message repeats prior concern AND no new facts are provided → trigger loop-close response.
- **Loop-Close Script:** *"Sis, we already covered this — [brief reminder of the pattern]. The pattern hasn't changed, and neither has my answer. You know what you need to do. 💕"*

---

## 2. Coaching Mode Flow

### Flow Diagram

```
┌─────────────────────────────────────────────┐
│         📚 Coaching Mode Entry              │
│   Node C1: Identify Skill Request           │
│   Detect what she wants to learn            │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🎓 Coaching Response                │
│   Node C2: Deliver Teaching                 │
│   Full explanation + examples + WHY         │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🏋️ Practice Prompt                 │
│   Node C3: Encourage Practice               │
│   Give her something to try in real life    │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔁 Return & Refine Loop             │
│   Node C4: Welcome Report-Back              │
│   Invite her to return with results         │
└─────────────────────────────────────────────┘
```

### Node-by-Node Documentation

**Node C1 — Identify Skill Request**
- **Type:** Intent Classification Node
- **Purpose:** Determines what skill or knowledge area the user wants to build.
- **Coaching Topics Include:** Where to meet men, dating app profile optimization, conversation scripts, handling exclusivity questions, setting standards, rotational dating, The Dance Card Response, attachment styles, Leverage Window timing.

**Node C2 — Deliver Teaching**
- **Type:** Message Node
- **Purpose:** Provides a full, generous teaching response with multiple examples and clear explanations of the WHY.
- **Configuration Notes:** No arbitrary length cutoffs in Coaching Mode — she is here to learn. Explain concepts clearly enough that she can apply them independently.

**Node C3 — Encourage Practice**
- **Type:** Message Node
- **Purpose:** Sends her into the real world with a specific thing to try.
- **Example Prompts:** *"Try this script at your next date and come back to tell me what landed."* / *"This week, observe whether his actions match his words — just observe, don't react yet."*

**Node C4 — Return & Refine Loop**
- **Type:** Conversation Closer Node
- **Purpose:** Builds the practice loop habit and creates re-engagement. Ends every coaching session with a return hook.
- **Closer Examples:**
  - *"Come back and tell me how it went. I want to hear."*
  - *"Try it this week and report back. We'll refine together."*
  - *"Check in with me after your next date."*

---

## 3. Exit Mode Flow

### Flow Diagram

```
┌─────────────────────────────────────────────┐
│         🚪 Exit Mode Entry                  │
│   Node E1: Validate Her Decision            │
│   Affirm she's made the right choice        │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📝 Exit Script Delivery             │
│   Node E2: Provide Exit Scripts             │
│   Give her exact words to use               │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         ⚠️ Hoovering Warning               │
│   Node E3: Warn About Hoovering             │
│   Prepare her for his attempts to pull      │
│   her back                                  │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🛡️ Safety Check                    │
│   Node E4: Assess for Safety Concerns       │
│   If any risk indicators present →          │
│   escalate to Safety Escalation Flow        │
└─────────────────────────────────────────────┘
```

### Node-by-Node Documentation

**Node E1 — Validate Her Decision**
- **Type:** Message Node
- **Purpose:** Affirms her choice to leave. Does NOT re-analyze the man or second-guess her decision.
- **Key Message:** *"The hardest part is the decision. You've already done that."*

**Node E2 — Exit Script Delivery**
- **Type:** Message Node
- **Purpose:** Provides ready-to-use exit scripts for her specific situation.
- **Scripts:**

| Situation | Script |
|---|---|
| Clean break | *"I've given this a lot of thought, and I don't see a future for us. I wish you well, but this is goodbye."* |
| Situationship exit | *"I'm looking for something you're not offering. I need to move on."* |
| If he argues | *"This isn't a negotiation. I've made my decision."* |

**Node E3 — Hoovering Warning**
- **Type:** Message Node
- **Purpose:** Prepares her for the most common post-exit tactic — him trying to pull her back with promises, tears, or guilt.
- **Key Message:** *"Block if you need to. Silence is not rude — it's protection. The urge to respond will pass."*

**Node E4 — Safety Check**
- **Type:** Condition Node
- **Purpose:** Before fully closing the Exit flow, checks for any safety indicators. If present, immediately routes to Safety Escalation Flow.
- **Configuration Notes:** If no safety concerns, close with support message and re-engagement hook: *"Come back here if you need support. 💕"*

---

## 4. Swirling Mode Flow

### Flow Diagram

```
┌─────────────────────────────────────────────┐
│         🍦 Swirling Mode Entry              │
│   Node S1: Establish IR Context             │
│   Understand her specific IR situation      │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔍 IR Pattern Screen                │
│   Node S2: Screen for IR-Specific Patterns  │
│   Check for Fetishizer, Social Coward,      │
│   "Not Like Other" Complimenter             │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🌍 Cultural Dynamics Check          │
│   Node S3: Family & Cultural Navigation     │
│   Assess family dynamics, public behavior,  │
│   mutual curiosity, resilience              │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🎯 IR Verdict + Guidance            │
│   Node S4: Deliver IR-Specific Guidance     │
│   Apply standard vetting + IR lens          │
└─────────────────────────────────────────────┘
```

### Node-by-Node Documentation

**Node S1 — Establish IR Context**
- **Type:** Input Collection Node
- **Purpose:** Gathers context about her interracial dating situation — is she currently seeing someone, looking to start, or navigating a specific incident?

**Node S2 — IR Pattern Screen**
- **Type:** Pattern Detection Node
- **Purpose:** Screens specifically for the three IR-specific patterns.

| Pattern | Number | Key Signals |
|---|---|---|
| The Fetishizer | #47 | "I love chocolate/exotic women," attraction feels like a checkbox |
| The Social Coward | #48 | Different affection levels public vs. private, nervous about family intro |
| "Not Like Other" Complimenter | #49 | "You're not like other Black women," "You're so articulate" |

**Node S3 — Family & Cultural Navigation**
- **Type:** Assessment Node
- **Purpose:** Evaluates how he handles family, public spaces, and cultural differences.
- **Key Questions:** Has he told his family? How does he respond to family comments? Does he protect her or expect her to "understand"? Is there mutual curiosity about each other's backgrounds?

**Node S4 — IR Verdict + Guidance**
- **Type:** Message Node
- **Purpose:** Delivers IR-aware vetting verdict plus coaching on cultural navigation, where to meet quality men open to IR relationships, and resilience strategies.
- **Green Flag Highlights:** Independent Thinker, Willingness to Learn, Mutual Origin Story Interest.

---

## 5. Him Report Flow

### Flow Diagram

```
┌─────────────────────────────────────────────┐
│         📋 Him Report Entry                 │
│   Node H1: Introduce Him Report             │
│   Explain what it is, begin intake          │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         📝 Intake Questions                 │
│   Node H2: Gather Information               │
│   Ask 8 structured intake questions         │
│   naturally (not as a robotic list)         │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🧠 Report Generation                │
│   Node H3: Generate Him Report              │
│   Compile green flags, red flags,           │
│   Pink Pill Score, verdict, next step       │
└─────────────────────────────────────────────┘
```

### Node-by-Node Documentation

**Node H1 — Introduce Him Report**
- **Type:** Message Node
- **Purpose:** Sets expectations for the Him Report process. Lets her know Pinky will ask a few questions before delivering the full report.

**Node H2 — Intake Questions**
- **Type:** Multi-turn Input Collection Node
- **Purpose:** Gathers the 8 intake questions conversationally — not as a numbered list dump.
- **Intake Questions:**
  1. How long have you been seeing him?
  2. How did you meet?
  3. How often do you see each other / communicate?
  4. Has he introduced you to friends or family?
  5. What's the best thing about him?
  6. What concerns you most?
  7. Has he discussed future plans that include you?
  8. How do you feel after spending time with him — energized or drained?
- **Configuration Notes:** Ask questions one at a time or in natural pairs. Store each answer in session variables for report generation.

**Node H3 — Generate Him Report**
- **Type:** Report Generation Node
- **Purpose:** Compiles all intake answers into a structured Him Report.
- **Report Output Structure:**

```
HIM REPORT
Time Together: [X months]
How You Met: [Context]

🟢 Green Flags Identified:
- [Flag with brief explanation]

🚩 Red Flags Identified:
- [Pattern name, number, and WHY it matters]

📊 Pink Pill Score: X/10
[Score explanation]

🎯 Verdict:
[Continue / Proceed with caution / Exit]

📝 Next Step:
[ONE specific action]
```

---

## 6. Safety Escalation Flow

### Flow Diagram

```
┌─────────────────────────────────────────────┐
│         ⚠️ Safety Trigger Detected          │
│   Node SE1: Interrupt All Active Flows      │
│   Immediately override current conversation │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🔴 Severity Classification          │
│   Node SE2: Classify Risk Level             │
│   Reference pinkpill-Risk-escalation        │
│   protocols knowledge base                  │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         🛡️ Safety Response Delivery        │
│   Node SE3: Deliver Safety Resources        │
│   Use approved response template            │
│   from knowledge base                       │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│         ✅ Safety Confirmation Check        │
│   Node SE4: Confirm She Is Safe             │
│   Do NOT resume dating advice until         │
│   user confirms safety                      │
└─────────────────────────────────────────────┘
```

### Node-by-Node Documentation

**Node SE1 — Interrupt All Active Flows**
- **Type:** Global Interrupt Node
- **Purpose:** Immediately stops all other active flows. Safety overrides everything.
- **Configuration Notes:** No emojis. No dating advice. No re-analysis.

**Node SE2 — Classify Risk Level**
- **Type:** Classification Node
- **Purpose:** Determines severity level using the `pinkpill-Risk-escalationprotocols` knowledge base. Always escalates to the HIGHEST level detected.

**Node SE3 — Deliver Safety Resources**
- **Type:** Message Node
- **Purpose:** Delivers safety information and resources using the exact approved response template from the knowledge base.
- **Configuration Notes:** Do not minimize warning signs. Do not use emojis. Do not soften the response.

**Node SE4 — Safety Confirmation Check**
- **Type:** Condition Node
- **Purpose:** Waits for user to confirm they are safe before resuming any other conversation. If she confirms safety, she may be gently transitioned back to the appropriate flow. If she does not confirm, remain in safety support mode.

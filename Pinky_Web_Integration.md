# Web Integration Documentation
## Pinky — Web Deployment

---

## Overview

Pinky is deployed as an embedded chat widget on a web page using Botpress's web channel. This document covers the setup, configuration, and customization of the web integration, including the Supabase subscription check, message quota enforcement, n8n webhook integration, and Constant Contact sync.

---

## Deployment Method

| Property | Value |
|---|---|
| **Platform** | Botpress |
| **Channel** | Web (Embedded Chat Widget) |
| **Embed Type** | JavaScript snippet injected into webpage |
| **Widget Trigger** | Auto-open on page load OR click-to-open button |
| **Subscription Backend** | Supabase |
| **Automation Layer** | n8n |
| **Email CRM** | Constant Contact |

---

## Embed Setup

### Step 1 — Get Your Botpress Embed Script

In your Botpress dashboard:
1. Navigate to your bot's **Integrations** tab
2. Select **Web Chat**
3. Copy the generated embed snippet

The snippet will look similar to this:

```html
<script>
  window.botpressWebChat.init({
    composerPlaceholder: "Chat with Pinky...",
    botConversationDescription: "Your personal vetting partner & dating strategist",
    botId: "YOUR_BOT_ID",
    hostUrl: "https://cdn.botpress.cloud/webchat/vX",
    messagingUrl: "https://messaging.botpress.cloud",
    clientId: "YOUR_CLIENT_ID",
    webhookId: "YOUR_WEBHOOK_ID",
    botName: "Pinky 💕",
    avatarUrl: "YOUR_AVATAR_IMAGE_URL",
    phoneNumber: "",
    emailAddress: "",
    website: "",
    coverPictureUrl: "YOUR_COVER_IMAGE_URL",
    termsConditions: "",
    privacyPolicy: "",
    useSessionStorage: false,
    enableConversationDeletion: false,
    showPoweredBy: false,
    theme: "prism",
    themeColor: "#E75480"
  });
</script>
<script src="https://cdn.botpress.cloud/webchat/vX/inject.js"></script>
```

### Step 2 — Place the Snippet in Your Web Page

Paste the embed snippet just before the closing `</body>` tag on any page where Pinky should appear.

---

## Configuration Options

| Property | Recommended Value | Notes |
|---|---|---|
| `botName` | `"Pinky 💕"` | Displayed in the chat header |
| `avatarUrl` | Pinky brand avatar URL | Upload brand image to CDN or Botpress assets |
| `coverPictureUrl` | Pinky brand cover image URL | Shown at top of chat panel |
| `composerPlaceholder` | `"Chat with Pinky..."` | Placeholder text in message input |
| `themeColor` | `"#E75480"` | Pink brand color |
| `theme` | `"prism"` | Botpress base theme |
| `showPoweredBy` | `false` | Hide "Powered by Botpress" branding |
| `useSessionStorage` | `false` | Use persistent storage for returning user detection |
| `enableConversationDeletion` | `false` | Prevent accidental deletion of conversation history |

---

## Subscription & Access Model

### Access Tiers

Pinky operates on a four-tier access model. All tiers are verified via Supabase on every message.

| Access Level | Daily Message Limit | Behavior |
|---|---|---|
| **Free Trial** (1 week, one-time) | Unlimited | Full access to all features and tiers |
| **Trial Expired** | None | Upgrade prompt only — no advice delivered |
| **Premium** | 10 messages per day | Full access; hard stop at daily cap; resets at midnight |
| **Ultra Premium** | Unlimited (Pocket Pinky) | Full unrestricted access — no daily cap ever |
| **Ultra Consult Pack Active** | Unlimited until pack exhausted | Pack balance replaces daily cap entirely |

### Free Trial Logic

- Every new user receives a **1-week free trial** with full access to all features and tiers.
- The trial is **one-time only** — it does not reset under any circumstance.
- The trial start date (`trial_start_date`) is stored as a user-level variable in Botpress on first interaction and must persist across sessions.

### Premium Daily Message Limit

- Premium subscribers may send up to **10 messages per day**.
- The counter (`daily_messages_used`) increments on every message after the subscription check passes.
- At 10 messages, the Daily Limit Wall fires and the session ends for that day.
- The counter resets to `0` at **midnight server time** each day.
- The reset is tracked via `daily_reset_timestamp` — Botpress checks this on each message to determine whether a new day has begun.

**Daily Limit Wall Message:**
> "Hey Sis, you've hit your 10 daily messages for today! 💕 Your limit resets at midnight — come back tomorrow to keep the strategy going.
>
> Or, if you want to keep going right now, grab an Ultra Consult pack and pick up right where we left off.
>
> [Get 500 Messages — $50] [Get 1,000 Messages — $80]"

No dating advice, mode content, or PDF CTAs are delivered after this message in that message cycle.

### Pocket Pinky (Ultra Premium)

Ultra Premium subscribers have no daily message limit. Their `subscription_status` returns `ultra_premium_active` from Supabase, and the daily message quota check is skipped entirely for their sessions. The Pocket Pinky experience is automatic — no additional feature flag or button is required within the bot. Ultra Premium is identified and routed at the subscription check node.

### Ultra Consult Mode

Ultra Consult is a message pack add-on available for purchase by any Premium subscriber. It fully replaces the daily message cap until the pack balance is exhausted.

| Pack | Price | Messages |
|---|---|---|
| Starter Pack | $50 | 500 messages |
| Power Pack | $80 | 1,000 messages |

**How Consult Packs Work:**
- On purchase, Supabase updates `consult_pack_balance` to the purchased message count.
- On each message while a pack is active, Botpress decrements `consult_pack_balance` by 1.
- When `consult_pack_balance` reaches `0`, `subscription_status` automatically reverts to `premium_active` and the standard 10-message daily cap resumes.
- Multiple pack purchases stack — buying a new pack when balance remains appends to the existing balance (e.g., 200 remaining + 500 new = 700 balance).
- Consult packs do not expire by time — only by usage.

### Upgrade Prompt Behavior (Trial Expired)

> "Hey Sis, your free week is up! 💕 You've gotten a taste of what Pinky can do — now it's time to go premium and keep the strategy coming.
>
> Upgrade now to get unlimited access to vetting, coaching, Him Reports, and everything else.
>
> [Upgrade to Premium →]"

No dating advice, mode content, or PDF CTAs are delivered after this message in that message cycle.

---

## Supabase Integration

Botpress checks Supabase on **every incoming message** to verify the user's current subscription status and message quota.

### How It Works

1. On each message, Botpress makes an API call to Supabase passing the user's unique identifier.
2. Supabase returns the user's subscription status and relevant quota data.
3. Botpress stores the results in session variables and routes accordingly.

### Supabase Variables to Track

| Field | Type | Description |
|---|---|---|
| `user_id` | String | Unique identifier for the user |
| `trial_start_date` | Timestamp | Date of first interaction — never resets |
| `is_premium` | Boolean | Whether the user has an active paid subscription of any kind |
| `is_ultra_premium` | Boolean | Whether the user has an active Ultra Premium subscription |
| `subscription_status` | Enum | `free_trial_active`, `trial_expired`, `premium_active`, `ultra_premium_active`, `consult_pack_active` |
| `consult_pack_balance` | Integer | Remaining message count for active Ultra Consult pack |
| `daily_messages_used` | Integer | Messages sent by this user today (Premium only; resets at midnight) |
| `daily_reset_timestamp` | Timestamp | Timestamp of last midnight reset for daily counter |

### Configuration Notes

- Supabase must expose an endpoint or row-level access that Botpress can query per user on each message.
- The `trial_start_date` must be written to Supabase on the very first interaction and never overwritten.
- `consult_pack_balance` must be decremented atomically in Supabase on each message to prevent race conditions.
- When `is_ultra_premium` is `true`, Botpress must skip the `daily_messages_used` evaluation entirely.
- When `consult_pack_balance` reaches `0`, Supabase must update `subscription_status` back to `premium_active` automatically or on next message check.

---

## n8n Webhook Integration

When Supabase confirms any new paid subscription (Premium or Ultra Premium) for the first time, Botpress sends the user's data to an n8n webhook, which saves the subscriber's information in Constant Contact.

### Webhook Flow

```
Supabase confirms paid subscription (any tier)
        │
        ▼
Botpress detects new paid status (first time)
        │
        ▼
Botpress sends webhook POST to n8n
        │
        ▼
n8n receives data and adds contact to Constant Contact
```

### Data Sent to n8n

| Field | Description |
|---|---|
| `name` | User's full name |
| `email` | User's email address |

> Only Name and Email are sent. No session data, conversation history, tier information, or subscription tier detail is included.

### n8n Webhook Setup

- Configure a webhook trigger node in n8n to receive the POST request from Botpress.
- The n8n workflow should:
  1. Receive the webhook payload (name + email)
  2. Check if the contact already exists in Constant Contact
  3. Add or update the contact in Constant Contact
- The webhook should only fire **once per user** — on the first confirmation of any paid status, not on every subsequent message.

### Configuration Notes

- Store a user-level variable `webhook_sent` (boolean) in Botpress to prevent duplicate webhook calls.
- Set `webhook_sent = true` after the first successful POST to n8n.
- If the webhook call fails, retry once before logging the failure — do not interrupt the user's session.
- Ultra Consult pack purchases do not trigger a new webhook — only the initial paid subscription confirmation does.

---

## Custom Swirl Icon

The Swirling mode button uses a **custom chocolate/vanilla swirl cone icon** (`swirl-icon.svg`), not a standard emoji.

**Setup:**
1. Upload `swirl-icon.svg` to your Botpress bot's media assets or a publicly accessible CDN
2. Reference the asset URL when configuring the Swirling quick-reply button in Botpress
3. Do NOT substitute a standard ice cream emoji — the custom icon is a brand asset

---

## Returning User Detection

To display the correct welcome message (new user vs. returning user), Pinky relies on the `returning_user` session variable.

**How it works:**
- On first visit, `returning_user = false` → display full welcome message
- On return visits, `returning_user = true` → display short welcome-back message

**Botpress Configuration:**
- Use Botpress's built-in user identity or a fingerprint/cookie mechanism to persist the `returning_user` flag across sessions
- Set `useSessionStorage: false` in the embed config so conversation data persists beyond a single browser session

---

## Mobile Responsiveness

The Botpress web chat widget is mobile-responsive by default. Confirm the following for Pinky's deployment:

- Test widget on iOS Safari, Android Chrome, and desktop browsers
- Ensure the custom swirl icon renders correctly at small sizes on mobile
- Confirm quick-reply buttons are tappable on touch screens (minimum 44px touch target)
- Verify the chat panel does not obscure page content on mobile
- Test that the Daily Limit Wall message and Ultra Consult upsell buttons render correctly on small screens

---

## Analytics & Tracking

| Event | Trigger | Purpose |
|---|---|---|
| `chat_opened` | User opens the widget | Measure engagement rate |
| `trial_started` | First interaction recorded | Track trial start |
| `trial_expired` | Upgrade prompt shown | Track conversion opportunities |
| `premium_activated` | Supabase confirms Premium payment | Track conversions |
| `ultra_premium_activated` | Supabase confirms Ultra Premium payment | Track high-value conversions |
| `consult_pack_purchased` | Supabase registers new pack balance | Track Ultra Consult revenue |
| `daily_limit_reached` | Premium user hits 10 messages | Track upsell opportunities |
| `consult_pack_exhausted` | Pack balance reaches 0 | Track re-purchase opportunities |
| `webhook_sent` | n8n webhook fired | Confirm Constant Contact sync |
| `mode_selected` | User clicks a mode button | Track most popular modes |
| `him_report_requested` | Him Report button clicked | Track high-intent users |
| `swirling_activated` | Swirling mode triggered | Track IR audience size |
| `safety_escalated` | Safety flow triggered | Monitor safety incidents |
| `pdf_guide_cta_shown` | PDF CTA message sent | Track monetization touchpoints |
| `session_count` | Per session | Feed into tier milestone logic |

**Integration Options:**
- Botpress Analytics dashboard (built-in)
- Google Analytics via custom event push from Botpress
- Segment or Mixpanel for advanced user journey tracking

---

## Deployment Checklist

Before going live, confirm the following:

- [ ] Botpress bot is published and set to Live
- [ ] Embed snippet is placed on all target web pages
- [ ] `botId`, `clientId`, and `webhookId` are correct and active
- [ ] Avatar and cover images are uploaded and rendering
- [ ] Custom `swirl-icon.svg` is uploaded and displaying on Swirling button
- [ ] Theme color matches Pinky brand (#E75480 or approved brand color)
- [ ] `showPoweredBy` is set to `false`
- [ ] Supabase connection is live and returning correct subscription statuses
- [ ] `trial_start_date` is being written to Supabase on first interaction
- [ ] Trial expiry logic is tested — upgrade prompt appears after 7 days with no payment
- [ ] Premium activation is tested — full access granted with 10-message daily cap active
- [ ] Daily limit logic is tested — limit wall fires at message 10, resets at midnight
- [ ] Ultra Premium activation is tested — no daily cap, Pocket Pinky active immediately
- [ ] Ultra Consult pack purchase is tested — balance decrements per message, reverts to Premium cap at 0
- [ ] Consult pack stacking is tested — new pack purchase adds to remaining balance correctly
- [ ] Daily Limit Wall upsell buttons are rendering and linking correctly
- [ ] n8n webhook is receiving POST from Botpress on first paid subscription confirmation
- [ ] Constant Contact is receiving Name + Email from n8n correctly
- [ ] `webhook_sent` flag prevents duplicate webhook calls
- [ ] Safety escalation bypasses all subscription and quota checks — tested end-to-end
- [ ] Returning user detection is working (test with two sessions)
- [ ] Mobile rendering is verified on iOS and Android
- [ ] PDF guide CTAs are NOT appearing on first interaction, during trial-expired state, or at daily limit wall
- [ ] All session variables are initializing correctly on session start

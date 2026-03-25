# Web Integration Documentation
## Pinky — Web Deployment

---

## Overview

Pinky is deployed as an embedded chat widget on a web page using Botpress's web channel. This document covers the setup, configuration, and customization of the web integration.

---

## Deployment Method

| Property | Value |
|---|---|
| **Platform** | Botpress |
| **Channel** | Web (Embedded Chat Widget) |
| **Embed Type** | JavaScript snippet injected into webpage |
| **Widget Trigger** | Auto-open on page load OR click-to-open button |

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

## Paywall / Trial Expiry Logic

Pinky includes a trial expiry gate. When the following condition is true, the bot silently exits without sending any message:

**Condition:**
```
trailExpired == true AND freeConsultsUsed >= 3
```

**Behavior:**
- Output NOTHING
- Do not send a message
- Do not summarize or acknowledge
- Allow the workflow to transition silently to the next node

**Configuration Notes:**
- Store `trailExpired` and `freeConsultsUsed` as user-level variables in Botpress
- Increment `freeConsultsUsed` at the start of each new consultation session
- The paywall gate should be checked at the beginning of each session flow, before the welcome message is sent

---

## Mobile Responsiveness

The Botpress web chat widget is mobile-responsive by default. However, confirm the following for Pinky's deployment:

- Test widget on iOS Safari, Android Chrome, and desktop browsers
- Ensure the custom swirl icon renders correctly at small sizes on mobile
- Confirm quick-reply buttons are tappable on touch screens (minimum 44px touch target)
- Verify the chat panel does not obscure page content on mobile

---

## Analytics & Tracking

To monitor Pinky's performance, consider setting up the following tracking events:

| Event | Trigger | Purpose |
|---|---|---|
| `chat_opened` | User opens the widget | Measure engagement rate |
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
- [ ] Returning user detection is working (test with two sessions)
- [ ] Paywall / trial expiry logic is tested and silently gating correctly
- [ ] Safety escalation flow is tested end-to-end
- [ ] Mobile rendering is verified on iOS and Android
- [ ] PDF guide CTAs are NOT appearing on first interaction
- [ ] All session variables are initializing correctly on session start

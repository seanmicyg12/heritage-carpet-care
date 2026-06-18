# Heritage Carpet Care — AI stack setup (n8n)

This wires your website to a tiny back office that runs itself: an **AI receptionist** that answers
chat 24/7, and a **lead pipeline** that pings your phone the second someone asks for a quote. Then it
shows you how to bolt on **review requests**, an **outreach drafter**, and a **Facebook Messenger** bot
that all reuse the same brain.

Everything runs on the free tiers you already have. The only paid piece is the AI itself, and on
`gpt-4o-mini` your **$10 of OpenAI credit lasts essentially forever** for a one-person shop (math at the
bottom — it's thousands of conversations).

```
  Website (Netlify, free)                 n8n (your Render instance)            Outside services
  ┌──────────────────────┐   chat POST    ┌───────────────────────────┐   key   ┌──────────────┐
  │  chat widget         │ ─────────────► │ WF1 AI Receptionist        │ ──────► │  OpenAI      │
  │  quote form          │   lead POST    │ WF2 Lead Pipeline          │ ──────► │  ntfy (push) │
  └──────────────────────┘ ─────────────► │ WF3 Reviews / WF4 Outreach │         └──────────────┘
                                          └───────────────────────────┘
```

---

## 0. What you need (5 minutes, all free)

| Thing | Where | Notes |
|---|---|---|
| n8n running | your Render service `n8n` | You already deploy this from `seanmicyg12/n8n-docker-render`. Open it, log in with user `seanmicyg`. |
| OpenAI API key | https://platform.openai.com/api-keys | Starts with `sk-...`. This is **not** the Playground — it's the same account/credit, used over the API. |
| ntfy app | https://ntfy.sh (App Store / Play Store) | Free push notifications to your phone. No account needed. |

> **Heads up about Render's free tier:** it puts your n8n to sleep after ~15 min idle. The first chat
> after a nap can take 30–50 seconds to wake. Fix in **Step 5** with a free uptime pinger so the
> receptionist is always warm during business hours.

---

## 1. AI Receptionist — import & connect OpenAI

1. In n8n: **top-right menu ▸ Import from File ▸** choose `n8n/01-ai-receptionist.json`.
2. Open the **"OpenAI - chat completion"** node. Under **Credential for Header Auth**, click
   **Create New** and set:
   - **Name:** `Authorization`
   - **Value:** `Bearer sk-...your key...`  ← keep the word `Bearer` and a space before the key.
   Save. (This header is how OpenAI authenticates you. The key lives only in n8n, never in the website.)
3. Click **Save** (top right), then toggle the workflow **Active** (top right).
4. Open the **"Webhook - chat"** node and copy the **Production URL**. It looks like:
   `https://n8n-xxxx.onrender.com/webhook/carpet-chat`

> If the OpenAI node's **Body** looks empty, set **Body Content Type = JSON** and **Specify Body = Using
> JSON**, then paste the expression from the `jsonBody` field in the file. (It's preconfigured; this is
> just a fallback if your n8n version resets it on import.)

### Wire it into the website
Open `index.html`, find the `N8N` block near the top of the `<script>`, and paste your URL:

```js
const N8N = {
  leadWebhook: '',
  chatWebhook: 'https://n8n-xxxx.onrender.com/webhook/carpet-chat',   // ← paste here
};
```

That's it — the chat widget now talks to your receptionist. The bot's personality lives in
`SYSTEM_PROMPT` in the same file; edit it any time (pricing, towns, tone) and redeploy.

---

## 2. Lead Pipeline — get pinged the instant a quote comes in

Speed-to-lead is the whole game in your towns: the first company to call back usually wins the job.
This makes your phone buzz within seconds of a form submit.

1. **Import from File ▸** `n8n/02-lead-pipeline.json`.
2. Pick a **secret topic name** nobody can guess, e.g. `heritage-leads-7f3k9`.
3. Open the **"Push to phone (ntfy)"** node and replace `heritage-leads-CHANGE-ME` in the body with your
   topic. **Save**, then toggle **Active**.
4. On your phone, open the **ntfy app ▸ + ▸ Subscribe to topic ▸** enter the same topic name.
5. Copy the **"Webhook - lead"** Production URL (`.../webhook/carpet-lead`) into `index.html`:

```js
  leadWebhook: 'https://n8n-xxxx.onrender.com/webhook/carpet-lead',
```

Now every quote request emails you (via Web3Forms — set that key in the form too) **and** pushes a
one-line alert to your phone: `Name | Phone | Town | Service | When | Notes`. Tap it, call them back.

> **Optional — log every lead to a Google Sheet:** add a **Google Sheets ▸ Append Row** node after the
> webhook, connect your Google account, and map `{{$json.body.name}}`, `{{$json.body.phone}}`, etc. Now
> you have a free CRM you can sort and follow up from.

---

## 3. Deploy the website (free, 5 minutes)

1. Set the two `EDIT:` items in `index.html`: your **phone number** (search `(312) 555-0199`, several
   spots incl. the `tel:` links) and your **Web3Forms key** (free at https://web3forms.com — paste into
   the hidden `access_key` field so quote emails reach your inbox).
2. Go to https://app.netlify.com/drop and **drag `index.html` and `logo.svg`** onto the page. You get a
   live URL instantly. (Buy a domain later, ~$12/yr, and point it there.)

---

## 4. Test the whole loop

- **Chat:** open the site, ask "how much for 4 rooms?" → you should get a real answer in a sentence or two.
- **Lead:** submit the quote form → your phone should buzz via ntfy within seconds.
- If chat hangs ~40s the first time, that's Render waking up — fix next.

---

## 5. Keep n8n awake during business hours (free)

1. Go to https://cron-job.org (free) or UptimeRobot.
2. Create a monitor that GETs your n8n base URL `https://n8n-xxxx.onrender.com/` every **10 minutes**,
   say 7am–8pm. That keeps the free dyno warm so the receptionist answers instantly when it matters.

---

## 6. Bolt-ons (same brain, more leverage)

You don't need these on day one. Add them as you grow — each reuses the receptionist pattern.

### WF3 — Review Request (your #1 growth engine)
Google reviews are what sell you in Hinsdale/Oak Brook. Automate the ask:
1. New workflow: **Schedule Trigger** (daily) → **Google Sheets ▸ Read** your "jobs done" sheet →
   **Filter** rows cleaned 1 day ago and not yet asked → **HTTP Request to ntfy** (or an SMS/email node)
   reminding *you* to text them your Google review link. (Keep a human in the loop — a personal text
   from the owner converts far better than an automated blast, and stays within texting rules.)
2. Even simpler v1: after each job, you tap one ntfy "send review text" reminder. The workflow just
   makes sure you never forget — that's most of the win.

### WF4 — Outreach Drafter (AI writes, you send)
Compliant outreach = warm, opted-in, or your own network. No scraping, no cold spam (it gets you
banned and burns your name in towns where everyone knows everyone).
1. **Schedule/Manual Trigger** → **Google Sheets ▸ Read** a list of *realtors / past customers / people
   who asked but didn't book* → **HTTP Request to OpenAI** (copy the node from WF1) with a prompt like:
   *"Write a 3-sentence, warm follow-up to {{name}}, a {{relationship}} in {{town}}, from Heritage
   Carpet Care. Offer a move-in/move-out or seasonal refresh. No hard sell."* → write the draft back to
   a "Drafts" column.
2. You skim the drafts each morning and send the good ones yourself from your own number/email. AI does
   the typing; you keep the judgment and the relationship.

### WF5 — Facebook Messenger bot (reuses the receptionist)
Honest expectations: auto-replying in Messenger requires a **Facebook Page**, a **Meta Developer app**,
and Meta's review for the `pages_messaging` permission. It's a real afternoon of setup, not a toggle.
When you're ready:
1. Create the Page and a Meta app, add the **Messenger** product, subscribe the Page.
2. In n8n, make a **Webhook** node and give its URL to Meta as the Messenger webhook (Meta sends a
   verification GET — handle it with a small Respond node returning the `hub.challenge`).
3. From the message event, pull the text → **OpenAI node** (same as WF1, same `SYSTEM_PROMPT`) → **HTTP
   Request** back to the Graph API `/me/messages` to reply. Your receptionist now works inside Messenger.
- For posting/comments, do it manually or with Meta's own scheduler at first. Automated posting needs
  more permissions and adds little versus you posting one good before/after photo a week.

---

## 7. Cost math (why $10 is plenty)

`gpt-4o-mini` is about **$0.15 per million input tokens** and **$0.60 per million output tokens**.
A full back-and-forth chat with a visitor is roughly 1–3k tokens. Ballpark: **well under a tenth of a
cent per conversation** — your $10 covers thousands of them. Set a hard **usage limit** in the OpenAI
dashboard (Billing ▸ Limits) so a surprise can never run past your budget.

---

## 8. Troubleshooting

| Symptom | Fix |
|---|---|
| Chat says "trouble connecting" | Workflow not **Active**, wrong `chatWebhook` URL, or n8n asleep (Step 5). Check the n8n **Executions** tab for errors. |
| Browser console shows a **CORS** error | On both Webhook nodes, **Options ▸ Allowed Origins (CORS)** must be `*` (already set in the files). Re-save. |
| OpenAI node returns **401** | The Header Auth credential value must be exactly `Bearer sk-...` (word "Bearer", a space, then the key). |
| OpenAI returns **429 / quota** | Add credit or you've hit your usage limit. |
| First chat each morning is slow | Render free dyno waking up — the Step 5 pinger keeps it warm. |
| No ntfy buzz | App subscribed to the **exact** topic string? Topic still says `CHANGE-ME`? Workflow Active? |

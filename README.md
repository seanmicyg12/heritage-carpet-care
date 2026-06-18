<img width="440" height="131" alt="Heritage Carpet Care" src="https://github.com/user-attachments/assets/c94b3713-45ef-46d0-b743-f43a63daf6ec" />

# Heritage Carpet Care

A one-person, AI-run carpet cleaning business for Chicago's affluent west suburbs — Hinsdale, Oak Brook,
Glen Ellyn, Clarendon Hills, Burr Ridge, Western Springs, Wheaton, Naperville, and Downers Grove.
This repo is the whole operation: a single-file website plus an n8n back office that answers chat and
chases leads for you. Runs on free tiers; the only cost is pennies of OpenAI usage.

## What's in here

| File | What it is |
|---|---|
| [`index.html`](index.html) | The entire website — hero, before/after slider, pricing, quote form, and the AI chat widget. Drag onto Netlify to go live. |
| [`logo.svg`](logo.svg) | The brand mark. |
| [`n8n/01-ai-receptionist.json`](n8n/01-ai-receptionist.json) | Importable n8n workflow: chat widget → OpenAI → reply. The receptionist. |
| [`n8n/02-lead-pipeline.json`](n8n/02-lead-pipeline.json) | Importable n8n workflow: quote form → instant push alert to your phone. |
| [`n8n-setup.md`](n8n-setup.md) | Step-by-step to wire it all together, plus review/outreach/Facebook bolt-ons. |
| [`launch-plan.md`](launch-plan.md) | The business playbook: pricing, equipment, legal, marketing, 7-day plan. |

## How it fits together

```
  Website (Netlify, free)              n8n (Render, free)                 Services
  chat widget  ──── POST chat ───►  WF1 AI Receptionist  ──► OpenAI (gpt-4o-mini)
  quote form   ──── POST lead ───►  WF2 Lead Pipeline    ──► ntfy push to your phone
                                    + Web3Forms emails you the lead
```

The browser never holds an API key — n8n adds it server-side. The bot's personality lives in
`SYSTEM_PROMPT` inside `index.html`; edit it any time.

## Go live in ~15 minutes

1. **Edit `index.html`** — set your phone number and Web3Forms key (search for `EDIT:`).
2. **Stand up the AI** — follow [`n8n-setup.md`](n8n-setup.md): import the two workflows, paste your
   OpenAI key, connect ntfy, drop the two webhook URLs into the `N8N` block in `index.html`.
3. **Deploy** — drag `index.html` and `logo.svg` onto https://app.netlify.com/drop. Done.

See [`launch-plan.md`](launch-plan.md) for the business side — what to charge, what machine to buy, and
the first seven days.

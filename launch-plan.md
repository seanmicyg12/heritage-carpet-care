# Heritage Carpet Care — Launch Plan

A one-person, AI-run carpet cleaning business targeting Chicago's affluent west suburbs. Built to start
booking this week on near-zero cash. The website + n8n stack in this repo *is* your back office — no
GoHighLevel, no monthly SaaS stack.

> **Reality check on the numbers below:** prices, fees, and equipment ranges are starting points to make
> decisions against, not quotes. Verify current county fees and live listings yourself before you spend —
> they change, and I'd rather you trust your own check than a number in a file.

---

## 1. Positioning

You are the premium, neighborly, *insured* option in towns where a low price reads as "not serious."
Everything — the site, the logo, the way you answer the phone — should look like it belongs in those
driveways.

**Service area (a deliberate ring of money around home base):**
Hinsdale · Oak Brook · Glen Ellyn · Clarendon Hills · Burr Ridge · Western Springs · Wheaton ·
Naperville · Downers Grove.

**Excluded on purpose: Elmhurst.** That's home turf — you don't want family, parents' friends, or college
acquaintances seeing the business. This isn't just a marketing choice, it changes how you run Nextdoor
(see §4). Keep Elmhurst out of every profile, ad radius, and post.

---

## 2. Pricing (premium, simple, no surprise fees)

| Package | Price | What's in it |
|---|---|---|
| **Refresh** | **$179** | Up to 3 rooms, pre-spray + hot-water extraction, spot treatment, deodorizer |
| **Whole Home** | **$349** | Up to 7 areas, stairs & hallway, pet odor neutralizer, stain-guard on traffic lanes |
| **Rugs & Stairs** | **from $99** | Area/oriental rugs, stairs per flight, upholstery add-on, rug pickup/drop-off |

**Rules:** $149 minimum service charge · Stairs $3/step · Pet treatment $40/area · Wool & specialty rugs
quoted on site · Final quote confirmed before any work starts.

> **Do not copy cheap-market pricing.** The playbook this came from was cleaning £60 council flats. You're
> working $1M+ homes. Undercharging is the single most common way these businesses die in a wealthy market.

---

## 3. Equipment (your one real purchase — get it right)

- **Target a real used hot-water extractor with a wand and hose, roughly $400–$800** on Facebook
  Marketplace / OfferUp / local restoration-gear resellers. This is the whole business in one box.
- **Test it live with water before you pay a cent.** A machine that fails on job #1 burns a customer you
  can never get back in a word-of-mouth town. Watch it spray and extract.
- **Avoid the $100 portable.** It's the trap. Underpowered machines leave carpets wet and customers unhappy.
- Add: a quality pre-spray, a neutralizing deodorizer, a stain-guard, microfiber towels, and a corner
  guard. Keep a clean, professional kit — they notice.

**Vehicle question that shapes week 1:** if you have something that fits the machine + hoses, you can take
whole-home jobs immediately. If you're working around a small car, start with Refresh/rug jobs and grab a
folding wagon to haul gear from the curb.

---

## 4. The growth engine: reviews & neighbors, not ads

These towns run on recommendations, not Facebook ads. Spend your energy here first; only touch paid ads
once cash is flowing, and lock the radius to your exact ZIPs if you do.

1. **Google Business Profile** — claim it day one. It's free and it's where these customers check you.
   Get the address-service-area set to your nine towns. Reviews here are your storefront.
2. **Review flywheel** — after every job, the owner sends a short personal text with your Google review
   link. Three real reviews beat thirty fake ones. The n8n **Review Request** workflow (see `n8n-setup.md`)
   makes sure you never forget to ask.
3. **Nextdoor — as a Business Page, never your personal account.**
   ⚠️ Nextdoor verifies personal profiles by **home address** and ties you to your home neighborhood —
   which for you is **Elmhurst**. Posting the business from your personal profile outs you to exactly the
   people you're avoiding. Run a **Nextdoor Business Page**, keep your personal profile private, and never
   cross the two.
4. **Realtor partnerships** — move-in/move-out cleans are gold in these towns. A few agents who recommend
   you = steady premium work. The n8n **Outreach Drafter** writes warm intros for you to send yourself.
5. **Before/after photos** — the single most valuable thing you can post. Replace the website's placeholder
   tiles and the chat/Nextdoor posts with your own job shots as you get them.

---

## 5. Legal & money (DuPage County) — verify current specifics

- **Assumed Name / DBA:** Illinois requires registering a business name you operate under. In DuPage this
  goes through the **County Clerk** and typically involves a filing fee plus a **publication** requirement
  in a local paper. Check the DuPage County Clerk's site for the **current fee and exact steps** before
  filing.
- **Insurance:** general liability is non-negotiable in $1M+ homes — it's also a selling point ("fully
  insured" is already on your site). Get a quote before your first job.
- **Phone:** grab a free **Google Voice** number so your personal cell stays private and texts/calls route
  cleanly. Put that number everywhere on the site (search `(312) 555-0199` and replace).
- **Payments:** Stripe, Square, or even Zelle to start. Keep business money separate from personal from
  day one.

---

## 6. The free tech stack (replaces GoHighLevel)

| Job | Tool | Cost |
|---|---|---|
| Website + AI chat + quote form | `index.html` on Netlify | Free |
| Logo / brand | `logo.svg` | Free |
| AI receptionist, lead alerts, automations | **n8n** on Render | Free tier |
| The AI itself | **OpenAI `gpt-4o-mini`** | ~pennies; your $10 lasts ages |
| Lead email | Web3Forms | Free |
| Instant lead push to phone | ntfy | Free |
| Reviews / reputation | Google Business Profile | Free |

Full wiring instructions: **[`n8n-setup.md`](n8n-setup.md)**.

---

## 7. Your first 7 days

**Day 1 — Make it real.** File the DBA. Grab the Google Voice number. Claim the Google Business Profile.
Set the two `EDIT:` items in `index.html` (phone, Web3Forms key) and drag it onto Netlify — you're live.

**Day 2 — The machine.** Hunt Facebook Marketplace / OfferUp. Go see the best one, test it with water, buy it.

**Day 3 — Turn on the brain.** Follow `n8n-setup.md`: import the two workflows, paste your OpenAI key,
connect ntfy, drop the webhook URLs into the site. Test chat + a fake lead.

**Day 4 — Reputation.** Set up the Nextdoor **Business** Page (not personal — re-read §4). Line up your
first 2–3 reviews by offering a friend-of-a-friend in one of the nine towns a first clean at cost.

**Day 5 — Realtors.** List 10 local agents. Use the Outreach Drafter to write warm intros; send them yourself.

**Day 6 — First real jobs.** Take inbound from the site + Nextdoor. Photograph every before/after.

**Day 7 — Tighten.** Swap real photos and real reviews into the site. Set your OpenAI usage limit. Book
next week. Repeat.

---

## 8. The three things that kill this business — don't do them

1. **Undercharging.** Low price = "not serious" in these ZIPs. Hold your prices.
2. **A cheap machine.** One wet-carpet disaster ends your word-of-mouth before it starts. Buy quality, test first.
3. **Outing yourself on Nextdoor.** Business Page only. Keep Elmhurst and your personal profile out of it.

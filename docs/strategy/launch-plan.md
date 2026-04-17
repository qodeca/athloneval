# Athlon Eval – lean launch plan

**Audience**: Marcin + whoever helps ship iteration 0.
**Status**: draft v0.3, 2026-04-17. Rewritten from v0.1 to adopt a lean-iteration operating mode: ship something small in two weeks, check whether anyone cares, then decide whether to spend more. Iteration 0 decisions D1–D4 confirmed 2026-04-17 (§9).
**Operating principle**: small iterations, fast shipping, limited cost, explicit traction gates. No 12-month commitment. Each iteration has a hypothesis, a ceiling on spend, a kill criterion, and a defined shippable artefact.

The full scale-up plan (team, €350 K budget, 12-month phased execution, advisory board, paper submission, etc.) lives in Section 7 as a **reference for "what this grows into if it works"** – not a commitment.

---

## 1. Strategic thesis (why Athlon Eval positions Qodeca as #1)

Qodeca's fitness specialism is legible to existing clients but invisible to the wider market. Athlon Eval – the first public, task-level, fitness-operational LLM benchmark – is the shortest-path distribution mechanism for making that specialism unmissable. The benchmark is not the product; Qodeca's delivery practice is. Athlon Eval is how we get free inbound from operators, platform vendors, LLM labs, and academics, because a reproducible leaderboard is the single artefact all four audiences reference.

**Athlon Eval only positions Qodeca if people actually use it.** Iteration 0 exists to test that assumption for under €1 K before committing to anything bigger.

## 2. The iteration ladder

Each step is a separate go/no-go decision. The ceiling cost and duration are hard caps – if we overshoot either, we stop and reassess rather than push through.

| Iter | Hypothesis under test | Duration | Ceiling cost | Shippable artefact | Continue signal |
|---|---|---|---|---|---|
| **0 – Beachhead** | Anyone cares enough to engage | 2 weeks | €1 K + ~40 Marcin-hours | 20-item EN MCQ prototype + blog post + static leaderboard page | ≥3 substantive replies to cold outreach **or** ≥1 inbound from operator/vendor/academic |
| **0.5 – Second serve** (optional) | First traction attempt missed the right hook | 1 week | €300 | Revised landing + re-targeted outreach | Same as iteration 0 |
| **1 – Prove usefulness** | Traction is real, not polite | 4 weeks | €3 K | 50 EN + 20 PL MCQs, 10 Track F red-flag scenarios, public GitHub repo, newsletter #1 | ≥50 GitHub stars, ≥100 newsletter subs, ≥1 vendor or operator DM |
| **2 – Build a moat** | Qodeca can become the default reference | 6–8 weeks | €10–15 K | 100 items × 3 languages (EN/PL/DE), Track D synthetic corpus, 8–10 model leaderboard, first advisory board member | ≥500 stars, ≥500 newsletter subs, ≥1 press mention, ≥1 paid advisory conversation |
| **3 – Credibility launch** | Ready for public-launch event | 8–12 weeks | €30–50 K | 7-language Stage-1 (A+D+F), paper draft to arXiv, public launch event, 3-member advisory board | Paper submitted to venue, ≥2 chain operators citing internally, ≥3 fitness-press mentions |
| **4+ – Scale plan** | Move from traction to category ownership | — | See §7 | Recurring leaderboard, Stage-2 private tracks, conference talks, commercial wedges live | Measured annually against §7 targets |

**Iterations 0–2 total cap: ~€20 K over ~3 months.** That is the entire commitment being requested now. Everything beyond iteration 2 is reassessed with fresh data.

## 3. Iteration 0 in detail – the 2-week traction test

This is the only iteration that gets a detailed spec here. Future iterations get their own planning docs if and when the previous one clears its gate.

### Hypothesis
> If we publish 20 fitness-domain MCQs with real model scores behind a Qodeca-branded landing page and reach out to 15 contacts, at least three will reply with substantive input and at least one will be inbound interest from someone we didn't contact.

If that's false, the broader ambition is also false and we stop before spending real money.

### Deliverables (end of day 14)

1. **20 Track A MCQs (English only)** – 10 exercise science + ACSM FITT, 5 contraindications/safety, 5 nutrition. Hand-authored by Marcin + one SME reviewer, ~2 hours per item including review.
2. **One Python script** (≤100 lines) that runs the 20 MCQs against 5 models via OpenRouter: GPT-5, Claude Opus 4.7, Gemini 2.5 Pro, Llama 4 70B, Qwen 3. Outputs a CSV.
3. **Static landing page at the domain** – hero + one-paragraph explainer + 5-row leaderboard table + link to GitHub repo + email capture. Static HTML, no framework. Hosted on GitHub Pages or Cloudflare Pages, free.
4. **One public GitHub repo** (`qodeca/athloneval` already exists) with the 20 items, the script, the CSV, and a README explaining what's missing.
5. **One launch post** – Qodeca blog + Marcin's LinkedIn, day-10. Title candidate: "We scored five LLMs on 20 fitness coaching questions. Here's what we found."
6. **Cold outreach batch** – 15 hand-picked emails sent day-12: 5 Qodeca clients (Bay Club, EōS, SC Fitness, Solinca, PureGym Arabia contacts), 5 platform/vendor contacts (Mindbody, ABC Glofox, Technogym, Keepme, eGym), 5 academics (SportQA authors, PH-LLM authors, AWF Warszawa, Warsaw Medical University, one JMIR scoping-review author). Template below.
7. **One-page readout** (internal) on day-14 tallying replies, inbound, and what to do next.

### Outreach email template (cold, EN baseline)

> Subject: 20 LLMs questions on fitness – would this be useful to you?
>
> Hi [name],
>
> Qodeca is building an open benchmark that scores large language models on fitness-industry tasks – coaching safety, program plausibility, club operations. As a first step I published 20 scored questions today: [link].
>
> Before we invest further I'd like 10 minutes of your reaction: does a scorecard like this help [their role/company], or is it solving the wrong problem? No sales pitch, no follow-up unless you want one.
>
> – Marcin

### Iteration 0 scorecard (run on day-14)

| Signal | Green (continue to iter 1) | Amber (run iter 0.5) | Red (stop) |
|---|---|---|---|
| Substantive replies to the 15 emails | ≥3 | 1–2 | 0 |
| Inbound DMs / comments from non-targeted contacts | ≥1 | 0 but engagement on LinkedIn post | no engagement |
| Operator signal ("yes, we'd use this") | ≥1 | tentative maybe | none |
| LinkedIn launch post reactions | ≥50 | 15–49 | <15 |
| GitHub stars from non-Qodeca accounts | ≥10 | 3–9 | <3 |

Two greens → continue to iteration 1. One green + amber elsewhere → iteration 0.5. All amber/red → pause the programme, keep the research report as standalone thought leadership.

### Iteration 0 budget

| Line | Estimate |
|---|---|
| OpenRouter API credits (5 models × 20 items × 5 runs for reproducibility) | €50–100 |
| Domain / DNS (already bought) | €0 |
| Hosting (GitHub Pages / Cloudflare Pages) | €0 |
| Design (logo wordmark, social card) – use existing Qodeca assets or Nanobanana | €0–50 |
| SME reviewer honorarium (1 ACSM-certified coach, ~3 hours) | €150–300 |
| Marcin's time (~40 hours across 2 weeks) | opportunity cost, not cash |
| Contingency | €300 |
| **Total cash out** | **€500–750** |

### Iteration 0 team (confirmed 2026-04-17)

- **Marcin** – domain owner, item author-of-record, SME sourcing, outreach sender, launch-post editor/publisher, day-14 go/no-go decision maker
- **Claude (Opus 4.7 via Claude Code)** – all engineering (repo scaffolding, Python eval script, static landing page, CI, data pipelines), MCQ drafting for Marcin's review, launch-post drafting, outreach email drafting, internal day-14 readout drafting, basic graphics (via Nanobanana if needed)
- **External SME** (paid, ~3 hours, sourced per Appendix C spec) – independent medical/coaching review of the 20 items before launch

No Qodeca engineer time needed. No designer time budgeted (Claude handles basic visuals; if Marcin wants branded polish we add a Qodeca designer for ~2 hours in days 9–10). No advisory board, no academic partners, no PR agency. All deferred to iteration 2+.

## 4. Iteration 1 in brief (4 weeks, if iter 0 clears)

- Expand to 50 EN + 20 PL MCQs, Track F mini (10 red-flag scenarios)
- Public GitHub repo with code, datasets, CI, `CONTRIBUTING.md`
- First newsletter issue ("The Fitness AI Brief" #1) via Buttondown
- 5 additional targeted reach-outs (operators + academics)
- One conversation with AWF Warszawa / Warsaw Medical University about academic co-authorship (exploratory, no MOU yet)
- Spend target: €3 K all-in (mostly translator + SME fees for Polish + API credits)

## 5. Iteration 2 in brief (6–8 weeks, if iter 1 clears)

- 100 items × 3 languages (EN, PL, DE), Track D synthetic club corpus v0, 8–10 model leaderboard
- First advisory board member confirmed (target: external academic)
- First podcast appearance (Fitt Insider or Athletech Live)
- Quarterly blog format locked in: "State of AI in fitness"
- Spend target: €10–15 K

## 6. What "traction" means – the continue-vs-kill principle

The honest failure mode for a benchmark is "publishing theatre": we ship, a handful of people clap, and nothing commercial or academic follows. Iteration gates exist to detect that early.

| Kind of signal | What it looks like | What it doesn't look like |
|---|---|---|
| **Operator pull** | An operator asks "can you score our current vendor against this?" | LinkedIn likes from people who will never buy |
| **Vendor engagement** | An LLM vendor PM asks how to run their model or why their score is low | Reshare without comment |
| **Academic interest** | An academic replies saying they want to co-author, cite, or critique | Generic "interesting!" replies |
| **Press interest** | A journalist asks for a briefing or a quote | A share from a low-follower account |
| **Contributor interest** | A non-Qodeca GitHub account opens a PR or a substantive issue | Stars with no comments or PRs |

**Hard rule**: signals have to come from people outside Qodeca and outside Marcin's direct personal network. Friend-of-the-firm engagement does not count.

## 7. Scale-up reference plan – for iteration 3+ only

This is the ambitious version, **only relevant once iterations 0–2 have cleared**. It is intentionally short here because detail now is false commitment. If we reach iteration 3 the details get re-planned with fresh information.

### 12-month success targets (same as v0.1)

Paper on arXiv + accepted venue · ≥20 GitHub contributors · ≥15 models on leaderboard · ≥3 operators citing in RFPs · ≥2 new Qodeca engagements attributable to Athlon Eval · ≥5 named pieces in fitness press.

### Commercial wedges (activate at iteration 3+)

- **Vendor-selection advisory** – 4–8 weeks, €30–50 K per engagement, funnel for delivery
- **Private-track build** (Tracks B/C/E/G) – 8–16 weeks per track, €100–300 K, core product
- **Delivery QA gate** – 2–4 weeks, €15–30 K, recurring add-on to delivery engagements

### Rough 12-month envelope

€300–400 K all-in if iterations 0–2 clear. Iteration 0–2 cost (~€20 K) is already inside that envelope. The remaining €280–380 K is the iteration 3+ commitment, **explicitly deferred until we've earned the right to spend it.**

### What gets added at scale
- External advisory board (5–7 members)
- University co-authorship (AWF Warszawa + Warsaw Medical U)
- Full 7-language Stage-1 scope (A+D+F)
- Paper to JMIR / NAACL workshop / NeurIPS D&B
- Public launch event tied to a fitness-industry conference
- Monthly leaderboard refresh, quarterly "State of AI in fitness" report
- Commercial wedges and sales enablement
- Stage-2 private tracks (B, C, E, G) built inside client engagements

None of this gets scoped in detail now.

## 8. Risks specific to the lean approach

| Risk | Mitigation |
|---|---|
| **Too small to be noticed** (20 items feels like a toy, no-one engages) | The surprise finding is the hook, not the size – hold back one quotable number for the launch post (e.g. a model that fails a basic contraindication question). Also: the launch post is explicitly framed as "iteration 0 of something bigger" so the smallness is honest, not embarrassing |
| **Lean version locks in a cheap brand** (people remember "that fitness quiz on LinkedIn" rather than "the benchmark") | Every artefact from day 1 names "Athlon Eval" and "iteration 0" and points at the ambitious roadmap. Framing discipline: it's a first slice, not a one-off |
| **Iteration gates get fudged** (we're emotionally invested and call amber signals green) | Write the day-14 readout template before launching. Ask one external person (co-founder, advisor) to adjudicate. Pre-register the thresholds in §3 |
| **Competitor ships while we iterate** (Google / OpenAI publishes a fitness benchmark during our 3-month lean test) | We're staking the claim publicly from day 1, so even an iteration-0 artefact dated 2026-04-xx gives us priority citation. Competitor risk drops after iter 0 ships, not after the scale plan |
| **Qodeca bandwidth gets pulled by client work mid-iteration** | Iteration 0 is ~40 Marcin-hours + ~10 Qodeca engineer-hours across 2 weeks. If that can't be ring-fenced, the whole programme cannot ship – better to know now |
| **Outreach list reveals no-one cares** | That is the intended outcome of the test. A clean negative is a €1 K purchase of strategic clarity |

## 9. Decisions – confirmed 2026-04-17

| # | Decision | Resolved value |
|---|---|---|
| **D1** | Iteration 0 budget | €1 K ceiling approved (expected spend €500–750) |
| **D2** | Domain | `athloneval.ai` (purchased; as per README) |
| **D3** | Engineering capacity | Claude (Opus 4.7 via Claude Code) does all engineering; no Qodeca human engineer assigned |
| **D4** | SME reviewer | To be selected against the specification in Appendix B before day 8 |

Everything downstream (stack choice for production scale, paper venue, advisory board, academic partners, licence model) remains deferred until iteration 2 at the earliest.

## 10. Iteration 0 shipping checklist (14 days)

### Days 1–3 – setup
- [x] D1–D4 confirmed (2026-04-17)
- [ ] Repo scaffolded by Claude: `pyproject.toml`, `ruff`, `pytest`, 1-page README, CI on GitHub Actions
- [ ] Landing page skeleton live at `athloneval.ai` (hero + "iteration 0 – launching [date]" placeholder, hosted via GitHub Pages or Cloudflare Pages)
- [ ] Outreach list drafted by Claude from Qodeca client list + vendor map + academic paper authors (15 names, emails verified by Marcin)
- [ ] SME reviewer sourcing started against Appendix C spec

### Days 4–8 – author
- [ ] 20 MCQs drafted by Marcin
- [ ] SME reviews all 20, marks up answers + rationale
- [ ] Items revised and frozen at `items_v0.json`
- [ ] Python script runs 20 items × 5 models × 5 repetitions → `runs_v0.csv`

### Days 9–10 – package
- [ ] Landing page updated with 5-row leaderboard, link to repo, email capture
- [ ] Launch blog post drafted (Qodeca blog + LinkedIn duplicate)
- [ ] Repo README finalised (what we ship, what we don't, what's next)

### Days 11–12 – launch
- [ ] Blog post + LinkedIn post go live
- [ ] 15 outreach emails sent in one batch (personalised per recipient)
- [ ] Post linked in one targeted Slack / Discord (fitness tech + ML evals communities)

### Days 13–14 – measure
- [ ] Tally replies, stars, subs, DMs against scorecard (§3)
- [ ] One-page internal readout
- [ ] Go/no-go decision for iteration 1

---

## Appendix A – deferred to later iterations

Explicitly out of scope for iterations 0–2:

- Advisory board formation
- University co-authorship and MOU
- Multilingual expansion beyond PL (iter 1) and DE (iter 2)
- Tracks B, C, E, G of any kind
- Paper submission to any venue
- PR agency engagement
- Formal pricing for commercial wedges
- Conference talk pitching (beyond reactive: if invited, accept)
- Non-profit or spin-out governance
- Detailed contamination-resistance scheme (iteration 2+)

## Appendix B – SME reviewer specification (iteration 0)

This is the exact spec against which Marcin sources the paid SME reviewer. Selection is deferred, but the criteria are fixed now so that when we source we source cleanly.

### Role
Independent external expert who reviews the 20 iteration-0 MCQs for medical and coaching correctness before public launch. Reviewer only – not a co-author, not part of future Athlon Eval governance (that's iteration 2+ territory).

### Required certification (at least one)
- **ACSM-CPT** (Certified Personal Trainer) or **ACSM-EP** (Certified Exercise Physiologist)
- **NSCA-CPT** or **NSCA-CSCS** (Certified Strength & Conditioning Specialist)
- **ACE-CPT** (American Council on Exercise – Certified Personal Trainer)
- **AWF Warszawa graduate** or **PZTA** (Polish equivalent) + 3+ years practice, if sourcing in PL
- **REPs / CIMSPA Level 3+** if sourcing in UK/EU

Evidence required: certification number + issuing body + current validity date.

### Experience
- Minimum 3 years active coaching practice in a commercial fitness / PT setting
- Has worked with general adult population including clients with at least one common chronic condition (hypertension, type 2 diabetes, lower-back issues, joint replacement) so contraindication flags are grounded in real practice, not textbook recall
- Not required: elite-athlete prep experience, clinical exercise physiology at research level, sports-medicine MD

### Domain knowledge
- **Must know**: ACSM FITT principles (frequency, intensity, time, type), ACSM exercise prescription guidelines (current edition), standard contraindications to exercise, basic nutrition for exercise (macronutrients, hydration, pre/post workout)
- **Nice to have**: familiarity with periodisation models, understanding of wearables-derived metrics (HR, HRV, sleep), experience with special populations (pregnancy, older adults)
- **Not required**: research-level biomechanics, clinical pharmacology, academic sports science

### Language
- **Native or C2 English** required (the 20 items are English-only for iter 0)
- Polish, German, or another Qodeca client-geography language is a bonus but not required

### Time commitment and rate
- **Total ~3 hours**: 2 hours to read + mark up 20 items, 30 min optional debrief call with Marcin, 30 min buffer
- **Turnaround**: within one week of receiving items
- **Rate range**: €80–120 per hour (market rate for certified coach doing expert review)
- **Budget envelope**: €240–360 total – within the €1 K iter-0 ceiling

### Neutrality requirements (non-negotiable)
- **Not** currently employed by any LLM vendor, fitness-tech platform, or Qodeca client (arm's-length principle)
- **Not** a current Qodeca employee or active Qodeca delivery contractor
- Personal training / coaching practice is fine; working for a gym chain Qodeca builds for is not

### Task deliverable
For each of the 20 MCQs the reviewer confirms, in writing:
1. The marked "correct" answer is actually correct
2. The distractors are plausible-but-wrong, not trivially wrong or ambiguously right
3. The authored rationale is medically/coaching-sound
4. The item is not dangerous, misleading, or ethically problematic

Per-item verdict: **approve** / **revise** (with notes) / **remove**.

Deliverable format: marked-up Google Doc or PDF emailed back to Marcin. Optional 20-minute debrief call.

### Confidentiality and attribution
- Standard reviewer NDA. Items remain under Qodeca control until public launch; post-launch they are CC-BY 4.0
- Reviewer's name listed in `CREDITS.md` in the public repo, if they consent. Otherwise anonymous "reviewed by an ACSM-certified coach"
- Reviewer is **not** positioned as an author of Athlon Eval. Reviewer only

### Sourcing channels (in preference order)
1. **Qodeca client-coach network** – a certified coach who personally trains at a Qodeca client club, but is not on that chain's payroll (independent PT contractor). Fastest, cheapest, relationship-backed.
2. **Personal network** – Marcin or Qodeca team asks one colleague or advisor to refer one certified coach.
3. **ACSM / NSCA / ACE public directory** – cold-contact for paid review work. Slowest, but fully independent and defensible in neutrality challenges.
4. **AWF Warszawa alumni network** (PL) – warm-intro via any existing Qodeca–AWF contact.

### Engagement template (brief to share with a candidate)
> Hi [name],
>
> Qodeca is publishing a small open benchmark for language models on fitness topics. Before we launch we'd like one independent certified coach to review 20 multiple-choice questions for medical and coaching correctness. The work is ~3 hours: reading and marking up 20 items plus an optional 20-minute debrief call. Fee €[240–360] depending on experience. Turnaround one week.
>
> You keep the right to decline attribution. Items become CC-BY 4.0 on launch day; everything before then is under NDA.
>
> Interested? I can send the items whenever you're ready.
>
> – Marcin

## Appendix C – key references from the research report

- **Part II §17** – the original "next steps" list, source of the one-week prototype idea
- **Part III §29.6** – the full 90-day plan that iteration 3 expands into
- **Part III §26.10** – stack recommendation (Python + Inspect AI) that we adopt starting iteration 1
- **Part I §5.1** – JMIR scoping review, the "why this benchmark matters" canonical reference
- **Part II §18** – the "academic theatre" objection the iteration gates are designed to surface early

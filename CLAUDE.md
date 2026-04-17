# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Current status: iteration 0 approved, engineering not yet started

**There is no code in this repository yet.** As of 2026-04-17 the repository contains the four-file research report under `docs/research/` (frozen 2026-04-11), a lean operational launch plan under `docs/strategy/`, this file, plus root-level `README.md` (public landing page), `LICENSE` (MIT), and `.gitignore`.

The **research report** (read-only unless adding new research findings; dated 2026-04-11):

- [`docs/fitness-llm-benchmarks-research.md`](docs/fitness-llm-benchmarks-research.md) — landing page, executive summary, methodology, TL;DR, navigation to the three parts
- [`docs/research/part-1-literature-survey.md`](docs/research/part-1-literature-survey.md) — academic benchmarks and studies (SportQA, SPORTU, SportR, Health-LLM, PH-LLM, HealthSLM-Bench, PHIA, NutriBench, SportsGPT, BiomechGPT, the 2025 JMIR scoping reviews, hypertrophy/marathon studies, gap analysis, academic design recommendations, reference list)
- [`docs/research/part-2-industry-frame.md`](docs/research/part-2-industry-frame.md) — fitness industry framing (Qodeca context, operator archetypes, platform layer, AI-native vendors, 12 real-world LLM workflows, industry gaps, Athlon Eval seven-track proposal, positioning options, risks)
- [`docs/research/part-3-benchmark-design-methodology.md`](docs/research/part-3-benchmark-design-methodology.md) — LLM benchmark design methodology backbone (eight-stage lifecycle, item authoring protocols, IAA targets, evaluation metrics, statistical rigor, contamination resistance, tooling landscape, governance, adjacent-domain lessons, and a concrete 90-day Stage-1 build plan for Tracks A, D, F). **The file a future implementer reads end-to-end for the long-horizon shape of the work.**

The **operational plan** (actively evolving; the canonical "what do we do next" document):

- [`docs/strategy/launch-plan.md`](docs/strategy/launch-plan.md) — lean iteration ladder (Iter 0 → 0.5 → 1 → 2 → 3 → scale), iteration-0 day-by-day checklist, SME reviewer specification (Appendix B), decision log (D1–D4 confirmed 2026-04-17). **This is where the next action lives.** It supersedes the "next steps" sketches in Part II §17 and Part III §29 by adding iteration gates, kill criteria, and a €1 K iter-0 scope.

### Repository metadata

- **Licence**: MIT ([`LICENSE`](LICENSE)) — applies to everything currently in this repo
- **Visibility**: public on GitHub as [`qodeca/athloneval`](https://github.com/qodeca/athloneval); commits are world-readable
- **Homepage**: [athloneval.ai](https://athloneval.ai)
- **Scope boundary**: this repo is the open core. Proprietary extensions (Tracks B, C, E, G per Part II Chapter 16) are developed outside this repo inside Qodeca delivery engagements

Any implementation work begins by reading **[`docs/strategy/launch-plan.md`](docs/strategy/launch-plan.md)** end-to-end – that is the current operational scope. For deeper background, then read Part II chapters 15–16 (the seven-track proposal), Part I part 5 (the JMIR scoping reviews that motivate the project), and Part III chapters 22 and 29 (benchmark lifecycle and the long-horizon 90-day Stage-1 plan that iteration 3+ grows into).

There is **no build system, test runner, lint config, package manifest, or Makefile yet**. Iteration-0 scaffolding is scoped and approved in the launch plan (D3, 2026-04-17) – a minimal Python 3.11+ package with `pyproject.toml`, `ruff`, `pytest`, an OpenRouter-driven eval script, and a plain-HTML landing page served via GitHub Pages. That is the full extent of the green light. **Do not exceed iteration-0 scope without explicit direction**: larger stack decisions (Inspect AI, monorepo layout, multilingual harness, judge ensembles, contamination tooling) are deferred to iteration 1+ and remain load-bearing choices to be made deliberately when the traction signal justifies them.

## What Athlon Eval is

`athlon-eval` is a planned **evaluation benchmark for large language models on fitness, sports, and fitness-industry operational tasks**. It is being designed by Qodeca (Warsaw-based software house, specialising in digital products for fitness/sport/healthcare operators) as a dual-purpose artefact:

1. An **open, peer-reviewable benchmark** that fills a gap explicitly called out by the 2025 JMIR scoping reviews on LLM exercise coaching — no existing benchmark tests end-to-end coaching quality, retention outreach, member concierge, or safety escalation in the fitness domain.
2. A **commercial delivery asset** for Qodeca — pre-sales differentiator, LLM vendor-selection tool for chain clients (Bay Club, EōS, PureGym Arabia, SC Fitness, Solinca, eGym, Jazzercise, ABC Glofox), and a delivery QA gate for Qodeca-built features.

## Architecture decisions already made (from the research report)

The following are documented in [`docs/research/part-2-industry-frame.md`](docs/research/part-2-industry-frame.md) (Chapters 15–16) as the recommended shape of the project. They are the current working assumptions — treat them as decisions unless the user rethinks them.

### Positioning: Option C – open core + proprietary augmentation

- **Public open-source tracks**: Knowledge (A), Operational Support (D), Safety & Compliance (F). Permissively licensed, published on GitHub, paper-worthy.
- **Private commercial tracks**: Program Generation (B), Member Conversation (C), Retention Outreach (E), Sales Assist (G). Used inside Qodeca client engagements.

This mirrors how MLPerf, HELM, and DeepEval manage the academic/commercial boundary.

### The seven tracks

| Track | Purpose | Primary consumer |
|---|---|---|
| A – Knowledge | MCQs on exercise science, ACSM FITT, contraindications, nutrition, club-ops terminology | Academic baseline, vendor comparison |
| B – Program generation | Periodised plan generation scored on ACSM grounding + expert rubric + reproducibility | PT copilot vendors |
| C – Member conversation | Open-ended multi-turn coach dialogue, scored on task success, accuracy, tone, safety | Member-app platforms |
| D – Operational support | Club FAQ/cancellations/billing grounded in a synthetic club knowledge corpus; deflection + escalation accuracy | Chain support; Mindbody-class platforms |
| E – Retention outreach | Given a churn-signal profile, draft personalised outreach | Keepme-class retention tools |
| F – Safety & compliance | Red-flag detection, medical escalation, contraindications, PII leakage, prompt-injection robustness | Go/no-go gate for any deployment |
| G – Sales assist | Objection handling, tour booking, lead qualification from a brand playbook | Sales-assist vendors |

### Cross-cutting requirements (non-negotiable in every track)

- **Multilingual** — minimum set EN, PL, DE, ES, FR, PT, AR (drawn from Qodeca's actual client geographies)
- **Self-hostable** — must run offline against any HTTP-compatible LLM endpoint; not tied to a single vendor SDK
- **Cost + latency reporting** — every score paired with dollar cost and p50/p95 latency
- **Reproducibility scoring** — run each scenario ≥5× and report variance (Hypertrophy-plan reproducibility paper PMC11963122 showed frontier models produce materially different plans for identical prompts)
- **Safety-gated** — Track F is a hard filter; models below threshold are excluded from the overall ranking rather than just scoring lower
- **Privacy-first** — every example synthetic or anonymised-with-consent; no real PII
- **Evidence-grounded** — RAG over ACSM, NSCA, WHO, EuropeActive sources plus a synthetic club-operations corpus
- **Rigor-aware** — self-scored on the JMIR Evaluation Rigor Score (ERS, 5-point), target ≥4/5

### Staged construction

- **Stage 1 (MVP, ~2 months)** — Tracks A + D + F only. Multilingual MCQs + red-flag scenarios + synthetic club-ops corpus. Leaderboard across at least 5 frontier + 5 open-weight models. Publishable as a paper and a reproducible GitHub repo.
- **Stage 2** — Add Tracks B, C, E with rubrics and expert panels.
- **Stage 3** — Add Track G + a longitudinal validation with a real chain (one of Qodeca's clients).
- **Stage 4** — Multimodal (video/form) and wearable time-series extensions (borrowing BiomechGPT / SportsGPT / Health-LLM / PH-LLM lineage).

**Work on Stage 1 scope first.** Don't build Stage 2+ tracks until Stage 1 is shipped and being used.

## Operational status (iteration 0 active)

The launch plan at [`docs/strategy/launch-plan.md`](docs/strategy/launch-plan.md) is the canonical tracker for iteration state. Current snapshot as of 2026-04-17:

- **Iteration 0 – beachhead**: approved, not yet started. 2-week traction test targeting 20 EN Track A MCQs × 5 models + static leaderboard + Qodeca blog + LinkedIn launch + 15 cold outreach emails. Day-14 scorecard decides iter-1 go/no-go.
- **Engineer**: Claude (Opus 4.7 via Claude Code) – all iter-0 engineering work runs through this agent per D3.
- **Budget**: ≤€1 K cash ceiling (expected spend €500–750) per D1.
- **Domain**: [athloneval.ai](https://athloneval.ai) purchased per D2.
- **SME reviewer**: selection pending against the specification in Appendix B of the launch plan per D4.
- **Kill criteria for iter-0**: pre-registered thresholds in launch-plan.md §3. If iter 0 fails the scorecard, we pause the programme and keep the research report as standalone thought leadership.

Beyond iteration 0 everything is explicitly deferred. Do not bring advisory board, academic co-authorship, multilingual scope, Tracks B/C/E/G, or paper-venue discussions into iter-0 work.

## Key references embedded in the research report

When reasoning about the benchmark design, these are the load-bearing academic references. Each lives in Part I at the section shown — read that section in [`docs/research/part-1-literature-survey.md`](docs/research/part-1-literature-survey.md) before consulting the external paper:

- **SportQA** (NAACL 2024, Part I §1.1) — closest existing sports-language benchmark, MCQ construction pipeline worth borrowing for Track A
- **PH-LLM** (Nature Medicine 2025, Part I §2.2) — Google's fitness/sleep LLM. Beat human experts 88% vs 71% on fitness MCQs but used closed Fitbit data. Its 857-case-study format is the template for Tracks B and C.
- **JMIR scoping review e79217** (2025, Part I §5.1) — the meta paper that motivates Athlon Eval; documents median Evaluation Rigor Score 2.5/5 across 20 studies and explicitly calls for a standardised benchmark. **Read this first if you're confused about why Athlon Eval exists.**
- **NutriBench** (2024, Part I §3.1) — adjacent, mature. The "When LLMs Can't Help" follow-up (arXiv:2511.20652) is the cautionary tale: intrinsic benchmark wins did not transfer to real-world deployment. Athlon Eval must plan for Stage-3 longitudinal validation.
- **BiomechGPT** (Part I §3.3) and **SportsGPT** (Part I §1.4) — the direction Stage-4 extensions would take.

When reasoning about *how* to build the benchmark (rather than what is in scope), the load-bearing references live in Part III's reference list at [`docs/research/part-3-benchmark-design-methodology.md`](docs/research/part-3-benchmark-design-methodology.md):

- **HELM** (Liang et al. 2022, arXiv:2211.09110, Part III §26.3) — the 7-metric × 16-scenario reporting taxonomy that every Athlon Eval track should publish.
- **Miller "Adding Error Bars to Evals"** (arXiv:2411.00640, Part III §25.2) — the canonical recipe for confidence intervals when sample size is in the thousands.
- **Bowyer et al. "Don't Use the CLT in LLM Evals"** (arXiv:2503.01747, ICML 2025 spotlight, Part III §25.1) — for Athlon Eval Tracks D and F (~150–500 items per language) the CLT under-estimates uncertainty; use Bayesian credible intervals.
- **MT-Bench / LLM-as-a-judge** (Zheng et al., arXiv:2306.05685, Part III §24.3-24.4) — the original LLM-as-judge paper; sets the bias mitigation requirements (position, verbosity, self-preference).
- **Inspect AI** (UK AISI, Part III §26.4) — the recommended primary runtime for Athlon Eval Tracks D and F.
- **LiveCodeBench** (arXiv:2403.07974, Part III §28.5) — the time-segmented, contamination-resistant pattern that Athlon Eval should adopt.
- **HarmBench + AgentHarm** (arXiv:2402.04249, arXiv:2410.09024, Part III §28.4) — harmful + benign prompt pairing, the structural pattern Track F must adopt.

## Industry context you should not forget

The benchmark is being designed against actual deployed systems, not hypothetical ones. Part II §12.2 has the full platform layer map; Part II §13 has the 12 workflows. Quick recall:

- **Technogym Mywellness Copilot** — automated program prescription on user profile + club coaching style
- **Mindbody Messenger[ai] + AI Assistant** — GPT-4 for 24/7 member concierge; "Clients At Risk" and "Big Spenders" ML models
- **ABC Glofox "At Risk" report** and **Keepme** — dedicated ML retention stacks, Keepme being the first fitness-sector ML retention platform
- **GymNation + LlamaIndex** — Albus (web/WhatsApp) + Jenny (voice sales). Public case study: 87% conversation rate, 75% tour booking, shipped by 4 people
- **Basic-Fit** — Speakap chatbot integration (employee-facing)

Task definitions in Athlon Eval must match these workflows closely enough that an operator can read a leaderboard row and know which platform to buy.

## How to work in this repository

Until code exists, almost all work is document work:

1. **Research/planning changes** — edit the correct file:
   - Landing page / exec summary / TL;DR → [`docs/fitness-llm-benchmarks-research.md`](docs/fitness-llm-benchmarks-research.md)
   - Academic benchmarks / papers / literature → [`docs/research/part-1-literature-survey.md`](docs/research/part-1-literature-survey.md)
   - Industry frame / Qodeca context / Athlon Eval proposal → [`docs/research/part-2-industry-frame.md`](docs/research/part-2-industry-frame.md)
   - Benchmark design methodology / tooling / statistical rigor / Stage-1 build plan → [`docs/research/part-3-benchmark-design-methodology.md`](docs/research/part-3-benchmark-design-methodology.md)
   - Operational plan / iteration status / iteration-0 checklist / SME spec / decisions log → [`docs/strategy/launch-plan.md`](docs/strategy/launch-plan.md)
   - Numbered sections (Parts 1–10 in Part I / Chapters 11–19 in Part II / Chapters 20–30 in Part III) are load-bearing — add new material as new sections rather than rewriting existing ones.
   - **Default file length**: docs files stay under 500 lines and split when they would exceed that.
   - **Exception**: Part III is intentionally up to ~1000 lines (currently 807) as the single methodology reference and is exempt from the split rule. Do not split it without explicit user direction — splitting would damage the cohesion that makes it useful as an end-to-end reference.
2. **Sources** — every factual claim is either directly verified from a paper/site or triangulated across ≥2 independent sources. Maintain that standard. Flag inferred vs. verified claims explicitly (global rule: "Verified: I read this in [X]" vs. "Inferred: Based on [Y]").
3. **Numerical precision** — the report's one acknowledged weakness is that some numbers come from web-fetched HTML abstracts rather than PDFs. If you're about to cite a number externally, spot-check it against the original PDF first.
4. **When you start writing code** — the user's global CLAUDE.md rules apply: branches not worktrees, Conventional Commits, `AskUserQuestion` for any clarifying decisions, sentence case (not Title Case), en dashes (not em dashes), responses ≤100 words unless the task is a multi-part deliverable.

## Do not

- Do not scaffold beyond iteration-0 scope. The iter-0 stack (Python 3.11+ with `pyproject.toml`, `ruff`, `pytest`, an OpenRouter eval script, plain-HTML landing page, GitHub Pages, GitHub Actions CI) is green-lit per D3 in the launch plan. Anything else — Inspect AI, monorepo split, multilingual harness, judge ensembles, contamination harness, framework-heavy front-ends — waits for iter-1 discussion.
- Do not treat recommendations in the research report (Part II chapters 15–16, Part III chapter 29) as decided commitments. They are options. The operational decisions live in [`docs/strategy/launch-plan.md`](docs/strategy/launch-plan.md) §9. When in doubt, ask.
- Do not expand Athlon Eval beyond Stage 1 scope in code until Stage 1 is shipped and being used by at least one reader of the leaderboard.
- Do not bring iteration 2+ activities (advisory board, academic partnerships, multilingual expansion beyond EN, paper submissions, PR campaigns) into iteration-0 work. They are explicitly deferred per the launch plan.

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Current status: pre-implementation (research phase)

**There is no code in this repository yet.** As of 2026-04-11, the project contains only the research report and this file. The report lives in `docs/` and is split into three files to stay under the 500-line-per-file convention:

- [`docs/fitness-llm-benchmarks-research.md`](docs/fitness-llm-benchmarks-research.md) — landing page, executive summary, methodology, TL;DR, navigation to the two parts
- [`docs/research/part-1-literature-survey.md`](docs/research/part-1-literature-survey.md) — academic benchmarks and studies (SportQA, SPORTU, SportR, Health-LLM, PH-LLM, HealthSLM-Bench, PHIA, NutriBench, SportsGPT, BiomechGPT, the 2025 JMIR scoping reviews, hypertrophy/marathon studies, gap analysis, academic design recommendations, reference list)
- [`docs/research/part-2-industry-frame.md`](docs/research/part-2-industry-frame.md) — fitness industry framing (Qodeca context, operator archetypes, platform layer, AI-native vendors, 12 real-world LLM workflows, industry gaps, Athlon Eval seven-track proposal, positioning options, risks)

Any implementation work begins by reading **Part II chapters 15–16** for the proposal and then **Part I part 5** (the JMIR scoping reviews) for the motivation.

There is intentionally **no build system, test runner, lint config, package manifest, or Makefile**. Do not invent or scaffold one without the user's explicit direction — the architecture choice (Python vs. TypeScript, monorepo vs. single package, evaluation framework) has not been made and is a load-bearing decision that should be taken deliberately.

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

## Key references embedded in the research report

When reasoning about the benchmark design, these are the load-bearing academic references. Each lives in Part I at the section shown — read that section in [`docs/research/part-1-literature-survey.md`](docs/research/part-1-literature-survey.md) before consulting the external paper:

- **SportQA** (NAACL 2024, Part I §1.1) — closest existing sports-language benchmark, MCQ construction pipeline worth borrowing for Track A
- **PH-LLM** (Nature Medicine 2025, Part I §2.2) — Google's fitness/sleep LLM. Beat human experts 88% vs 71% on fitness MCQs but used closed Fitbit data. Its 857-case-study format is the template for Tracks B and C.
- **JMIR scoping review e79217** (2025, Part I §5.1) — the meta paper that motivates Athlon Eval; documents median Evaluation Rigor Score 2.5/5 across 20 studies and explicitly calls for a standardised benchmark. **Read this first if you're confused about why Athlon Eval exists.**
- **NutriBench** (2024, Part I §3.1) — adjacent, mature. The "When LLMs Can't Help" follow-up (arXiv:2511.20652) is the cautionary tale: intrinsic benchmark wins did not transfer to real-world deployment. Athlon Eval must plan for Stage-3 longitudinal validation.
- **BiomechGPT** (Part I §3.3) and **SportsGPT** (Part I §1.4) — the direction Stage-4 extensions would take.

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
   - Numbered sections (Parts 1–10 / Chapters 11–19) are load-bearing — add new material as new sections rather than rewriting existing ones.
   - All docs files must stay under 500 lines; split to a new file in `docs/research/` if a file would exceed that.
2. **Sources** — every factual claim is either directly verified from a paper/site or triangulated across ≥2 independent sources. Maintain that standard. Flag inferred vs. verified claims explicitly (global rule: "Verified: I read this in [X]" vs. "Inferred: Based on [Y]").
3. **Numerical precision** — the report's one acknowledged weakness is that some numbers come from web-fetched HTML abstracts rather than PDFs. If you're about to cite a number externally, spot-check it against the original PDF first.
4. **When you start writing code** — the user's global CLAUDE.md rules apply: branches not worktrees, Conventional Commits, `AskUserQuestion` for any clarifying decisions, sentence case (not Title Case), en dashes (not em dashes), responses ≤100 words unless the task is a multi-part deliverable.

## Do not

- Do not scaffold a package without the user explicitly choosing a language/framework. The report does not mandate one.
- Do not treat any of the "recommended" items in the research report as decided. The user framed them as options to discuss, not decisions. When in doubt, ask.
- Do not expand Athlon Eval beyond Stage 1 scope in code until Stage 1 is shipped and being used by at least one reader of the leaderboard.
- Do not add build/CI/tooling files preemptively — the user may prefer a different stack than you'd guess.

# Fitness and sports LLM benchmarks – research report

**Prepared**: 2026-04-11
**Scope**: Comprehensive survey of existing, in-progress, and adjacent benchmarks that evaluate large language models on fitness, exercise, sports, and related health-coaching tasks – framed around Qodeca's fitness-industry delivery context.
**Purpose**: Inform the design of `athlon-eval` by mapping what exists, what's being worked on, and where the gaps are.

---

## How this report is organised

The report is split across three parts, each self-contained but cross-referenced:

| Part | File | What it covers |
|---|---|---|
| **Part I – Academic literature survey** | [`research/part-1-literature-survey.md`](research/part-1-literature-survey.md) | SportQA, SPORTU, SportR, Health-LLM, PH-LLM, HealthSLM-Bench, PHIA, NutriBench, SportsGPT, BiomechGPT, the two 2025 JMIR scoping reviews, hypertrophy/marathon expert studies, commercial coach products, gap analysis, academic-only design recommendations, and a 40-entry reference list. |
| **Part II – The fitness industry angle** | [`research/part-2-industry-frame.md`](research/part-2-industry-frame.md) | Qodeca company context, fitness-operator archetypes, platform layer (Technogym / Mindbody / ABC Glofox / Keepme / eGym), AI-native challenger vendors, 12 real-world LLM workflows at chains today, industry-specific evaluation gaps, cross-cutting requirements (GDPR/HIPAA, multilingual, brand voice, latency), the Athlon Eval seven-track proposal, strategic positioning options, next steps, risks, and an extended reference list. |
| **Part III – LLM benchmark design methodology** | [`research/part-3-benchmark-design-methodology.md`](research/part-3-benchmark-design-methodology.md) | The methodological backbone for Athlon Eval. Eight-stage benchmark lifecycle, item authoring protocols, inter-annotator agreement targets, evaluation metrics (closed-form + LLM-as-judge + Bradley-Terry), statistical rigor (Miller 2024, Bowyer 2025), contamination resistance, tooling landscape (lm-eval-harness, HELM, Inspect AI, DeepEval, Promptfoo, RAGAS), governance (datasheets, model cards, eval cards), lessons from medical/legal/multilingual/safety benchmarks, and a concrete 90-day Stage-1 build plan for Tracks A, D, F. |

Most readers should start with the executive summary below, then jump to the part relevant to their question. Builders should jump straight to Part III §29.

---

## Executive summary

The short answer: **yes, fitness and sports LLM benchmarks exist – but the landscape is fragmented, immature, and has a large hole exactly where a well-designed benchmark would be most valuable**.

Three near-disjoint families of work have emerged since early 2024:

1. **Sports-knowledge benchmarks** – MCQ/VQA datasets that test whether an LLM knows the rules, history, and tactics of team/individual sports. The canonical entries are **SportQA** (NAACL 2024), **SPORTU** (ICLR 2025), and **SportR** (late 2025). These measure comprehension, not coaching quality.
2. **Wearable-data health benchmarks** – datasets that test whether LLMs can reason over time-series sensor data to produce predictions, insights, or personalised advice. The leading entries are **Health-LLM** (MIT/Google, Jan 2024), **PH-LLM** (Google, Nature Medicine 2025), **HealthSLM-Bench** (Sept 2025), and **PHIA** (Nature Communications 2025). Fitness sits inside these alongside sleep, metabolic, and mental-health tasks – never in isolation.
3. **Coaching-quality case studies and scoping reviews** – roughly 20–30 peer-reviewed studies evaluating ChatGPT/GPT-4/Gemini on specific tasks (hypertrophy plans, marathon plans, cardiac rehab, physiotherapy advice), typically with 10–30 human experts rating outputs on Likert rubrics. A **2025 JMIR scoping review** (20 studies) explicitly concludes the field *lacks* a standardised benchmark and assigns a median "Evaluation Rigor Score" of **2.5/5**, with **55% classified as low-rigor**.

What's missing – and what a benchmark named `athlon-eval` could uniquely own – is a **structured, reproducible benchmark for end-to-end fitness coaching quality**: periodised program generation, evidence-grounding against ACSM/NSCA guidelines, adaptation to injury/fatigue signals, and safety guardrails. Every scoping review published in 2025 calls for exactly this, and no public benchmark currently provides it.

The industry angle (Part II) sharpens the picture further. Of the **twelve real-world LLM workflows** already deployed across fitness chains and their platforms (onboarding, concierge, retention outreach, PT copilot, sales assist, etc.), **ten have no public benchmark at all**. Commercial vendors publish outcome claims (retention %, conversion %) without model-level quality evidence; buyers have no way to compare. Qodeca's direct delivery access to operators like Bay Club, EōS, PureGym Arabia, SC Fitness, Solinca, and eGym places it in an unusually favourable position to build the standard that both sides of the market need.

---

## Methodology

This report synthesises findings from ~45 web searches and ~20 targeted paper and site fetches conducted on 2026-04-11. Sources covered: arXiv, PubMed Central, JMIR, Nature Medicine, Nature Communications, Nature Scientific Reports, NAACL/ICLR proceedings, Hugging Face datasets hub, commercial product announcements (WHOOP, Strava, Fitbit, Technogym, Mindbody), fitness-industry trade press, IHRSA and EuropeActive materials, and Qodeca's own public presence. Each claim was either read directly from the source or triangulated across at least two independent sources. Numerical precision from web-fetched HTML abstracts (as distinct from PDFs) should be spot-checked against the originals before external citation.

---

## Top-line findings (TL;DR)

1. **SportQA / SPORTU / SportR** are the only rigorous, public sports-language benchmarks – all test knowledge/comprehension, none test coaching quality. See Part I §§1.1–1.3.
2. **PH-LLM (Nature Medicine 2025)** is the most fitness-specific LLM evaluation to date and beat human experts 88% vs 71% on a fitness MCQ exam, but its 857 case studies and Fitbit data are closed. See Part I §2.2.
3. **Two 2025 JMIR scoping reviews** explicitly call for a standardised fitness-LLM benchmark framework. These are the load-bearing motivation papers. See Part I §§5.1–5.2.
4. **Nutrition benchmarks are 1–2 years ahead** of fitness (NutriBench, FoodSky, RD exam). Athlon Eval should borrow their playbook: large public datasets, domain-specialised models, real-world deployment follow-ups. See Part I §3.1.
5. **"When LLMs Can't Help"** (arXiv:2511.20652) is the cautionary tale – intrinsic-benchmark wins didn't transfer to real nutrition deployments. Athlon Eval must plan for Stage-3 longitudinal validation. See Part I §3.1.
6. **Fitness industry LLM deployments exist at scale** – Technogym Mywellness Copilot, Mindbody Messenger[ai], ABC Glofox "At Risk", Keepme, GymNation's Albus+Jenny stack – but none publishes a task-level quality benchmark. See Part II §12.2.
7. **Of 12 real-world fitness LLM workflows**, 10 have no public benchmark at all. The worst-covered areas are retention outreach, sales assist, and franchisee enablement. See Part II §14.
8. **Qodeca's recommended positioning** is Option C – open public core (Tracks A Knowledge, D Operational Support, F Safety) + proprietary augmentation (Tracks B, C, E, G). Stage-1 MVP shippable in ~2 months. See Part II §§15–16.

---

## Report status

- **Version**: 3 (Part I and Part II written 2026-04-11; Part III added 2026-04-11 in a follow-up session and fact-checked the same day)
- **Last updated**: 2026-04-11
- **Repository state**: pre-implementation. Beyond this report, the repo contains [`../README.md`](../README.md) (public landing page), [`../CLAUDE.md`](../CLAUDE.md) (Claude Code working notes), [`../LICENSE`](../LICENSE) (MIT), and `.gitignore`. The repo is public at [github.com/qodeca/athloneval](https://github.com/qodeca/athloneval). See `CLAUDE.md` for how future work should start.
- **Business metrics and vendor claims** drawn from trade press and vendor-published materials – treat them as directional rather than audited.
- **Academic numerical claims** (model scores, dataset sizes) verified against arXiv abstracts; spot-check original PDFs before citing externally. Part III went through a dedicated fact-check pass on 2026-04-11; claims are tagged inline as `[verified]` or `[inferred]`.
- **Part III file length**: 807 lines, deliberately exceeding the project's normal 500-line cap. As the methodology reference doc, splitting it would damage cohesion. Treat this as a single-file exception, not a precedent.
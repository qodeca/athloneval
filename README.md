# Athlon Eval

> LLM evaluation benchmark for fitness, sports, and fitness-industry operational tasks.

**Status**: Pre-implementation — research phase. No code yet. The problem space, literature survey, industry framing, and proposed benchmark design live in [`docs/`](docs/).

**Website**: [athloneval.ai](https://athloneval.ai)
**Maintained by**: [Qodeca](https://qodeca.com), Warsaw

---

## What is Athlon Eval?

An open + proprietary evaluation benchmark for large language models on fitness, sports, and fitness-industry operational workflows. It fills a gap explicitly identified in the [2025 JMIR scoping review on LLM exercise coaching](https://pmc.ncbi.nlm.nih.gov/articles/PMC12520646/): no existing public benchmark tests end-to-end coaching quality, retention outreach, member concierge, or safety escalation in the fitness domain.

The name comes from the Greek ἆθλον — "contest, prize" — the root that gives us *athlete*, *decathlon*, and *triathlon*.

## Why this benchmark?

Three near-disjoint families of academic work exist in the space, and none of them measures the thing operators actually care about:

1. **Sports-knowledge benchmarks** — [SportQA](https://arxiv.org/abs/2402.15862), [SPORTU](https://arxiv.org/abs/2410.08474), [SportR](https://arxiv.org/abs/2511.06499) test rule comprehension, not coaching quality.
2. **Wearable-data health benchmarks** — [Health-LLM](https://arxiv.org/abs/2401.06866), [PH-LLM](https://www.nature.com/articles/s41591-025-03888-0), [HealthSLM-Bench](https://arxiv.org/abs/2509.07260), [PHIA](https://www.nature.com/articles/s41467-025-67922-y) include fitness as one axis among many, and most use closed data.
3. **Coaching-quality case studies** — roughly 20–30 peer-reviewed studies with fragmented rubrics. The 2025 JMIR scoping review gives them a median Evaluation Rigor Score of 2.5 out of 5.

Meanwhile fitness-industry LLM deployments exist at scale — Technogym Mywellness Copilot, Mindbody Messenger[ai], ABC Glofox, Keepme, GymNation's LlamaIndex stack — but **none publish task-level quality evidence**. Of 12 real-world fitness LLM workflows mapped in the industry frame, 10 have no public benchmark at all.

Athlon Eval aims to be that benchmark.

## The seven tracks (proposed)

| Track | What it tests | Primary consumer |
|---|---|---|
| **A — Knowledge** | Exercise science, ACSM FITT, contraindications, nutrition (MCQ, multilingual) | Academic baseline, vendor comparison |
| **B — Program generation** | Periodised plans scored against ACSM rubric, with reproducibility scoring | PT copilot vendors |
| **C — Member conversation** | Multi-turn coach dialogue on goal elicitation, rationale, adaptation, motivation | Member-app platforms |
| **D — Operational support** | Club FAQ / cancellations / billing against a synthetic knowledge corpus | Chain support teams |
| **E — Retention outreach** | Personalised outreach drafted from churn-signal profiles | Retention platforms |
| **F — Safety & compliance** | Red-flag detection, medical escalation, PII leakage, prompt-injection robustness | Go/no-go gate |
| **G — Sales assist** | Objection handling, tour booking, lead qualification | Sales-assist vendors |

**Positioning**: open core + proprietary augmentation. Tracks A, D, F are planned for public release under a permissive licence; Tracks B, C, E, G as proprietary extensions used inside Qodeca delivery engagements. This mirrors how MLPerf, HELM, and DeepEval manage the academic/commercial boundary.

## Cross-cutting requirements

Every track is designed to be:

- **Multilingual** — EN, PL, DE, ES, FR, PT, AR minimum, drawn from Qodeca's actual client geographies
- **Self-hostable** — runnable offline against any HTTP-compatible LLM endpoint; not tied to a single vendor SDK
- **Cost + latency transparent** — every score paired with dollar cost and p50/p95 latency
- **Reproducibility-scored** — each scenario run ≥5× and variance reported
- **Safety-gated** — Track F is a hard filter; models below threshold are excluded from overall rankings
- **Privacy-first** — every example synthetic or consented-and-anonymised; no real PII
- **Evidence-grounded** — RAG over ACSM, NSCA, WHO, EuropeActive sources plus a synthetic club-operations corpus

## Current status

- [x] Literature review of ~40 related benchmarks and studies
- [x] Fitness-industry landscape map (operator tiers, platform layer, AI-native vendors, economics)
- [x] Twelve-workflow gap analysis
- [x] Seven-track proposal with methodological principles
- [x] Strategic positioning decision (open core + proprietary augmentation)
- [ ] Stage 1 MVP (Tracks A + D + F) — not yet started
- [ ] Expert panel for Tracks B and F
- [ ] Longitudinal validation with a chain client (Stage 3)

## Documentation

All substantive content is in [`docs/`](docs/):

- **[Landing page & executive summary](docs/fitness-llm-benchmarks-research.md)** — start here
- **[Part I — Academic literature survey](docs/research/part-1-literature-survey.md)** — SportQA, PH-LLM, Health-LLM, JMIR scoping reviews, NutriBench, BiomechGPT, and the ~40-entry reference list
- **[Part II — Fitness industry frame](docs/research/part-2-industry-frame.md)** — Qodeca context, chain operator tiers, platform layer, real-world LLM workflows, seven-track proposal, positioning options, risks
- **[Part III — LLM benchmark design methodology](docs/research/part-3-benchmark-design-methodology.md)** — eight-stage benchmark lifecycle, item authoring, evaluation metrics, statistical rigor (Miller 2024 / Bowyer 2025), contamination resistance, tooling landscape (lm-eval-harness, HELM, Inspect AI, DeepEval, Promptfoo, RAGAS), governance, lessons from adjacent domains, and a 90-day Stage-1 build plan for Tracks A, D, F

For Claude Code working notes on navigating this repo, see [`CLAUDE.md`](CLAUDE.md).

## Licence

MIT — see [LICENSE](LICENSE). This repository contains the public research and planning artefacts for Athlon Eval, released under a permissive licence in line with the open-core positioning in Part II Chapter 16. Any proprietary track extensions (B, C, E, G) will be developed as separate artefacts inside Qodeca delivery engagements, outside this repository.

## Contact

Maintained by [Qodeca](https://qodeca.com) — a Warsaw-based software house specialising in digital products for fitness, sport, and healthcare operators. For commercial engagements or benchmark collaboration enquiries: `hi@qodeca.com`.

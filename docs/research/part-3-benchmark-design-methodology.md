# Part III – LLM benchmark design methodology

*Part 3 of 3 of the fitness LLM benchmarks research report. Landing page lives in [`../fitness-llm-benchmarks-research.md`](../fitness-llm-benchmarks-research.md). The academic literature survey is [`part-1-literature-survey.md`](part-1-literature-survey.md). The industry framing is [`part-2-industry-frame.md`](part-2-industry-frame.md).*

*This part is the methodological backbone for Athlon Eval. Where Parts I and II answered "what is the gap?" and "who is the user?", Part III answers "how does anyone build a credible LLM benchmark in 2026?" – with every section landing on what the answer means for Athlon Eval Stage 1 (Tracks A, D, F). Compiled 2026-04-11 from ~25 web searches across academic methodology papers, framework documentation, and benchmark post-mortems.*

**Citation style**: claims are tagged inline as **[verified]** when read directly from the source paper, framework docs, or benchmark page; **[inferred]** when synthesised across multiple sources or extrapolated. Numbered sources live in [Chapter 30](#chapter-30--references).

**Why Part III is longer than Parts I and II**: this part is intended to be the load-bearing reference a Stage-1 builder reads end-to-end. It is the only part of the research report that the user explicitly asked to exceed the project's normal 500-line ceiling.

---

## Chapter 20 – Why this part exists

Part I documented that no fitness benchmark tests end-to-end coaching, retention, concierge, or safety. Part II documented that the same gap exists in industry – there is no operator-grade leaderboard a chain procurement team can use. Athlon Eval is the artefact that fills both gaps.

But "build a benchmark" is not a single decision. It is at least a dozen interlocking design choices, each of which has a literature, a community standard, and several known failure modes. A benchmark that ignores any of those choices either fails peer review (academic value zero) or fails to predict real-world deployment behaviour (commercial value zero). Both outcomes are catastrophic for Athlon Eval, because its dual-purpose positioning depends on both audiences trusting it.

The questions Part III answers, in order:

| # | Question | Athlon Eval decision it informs |
|---|---|---|
| 1 | What kind of artefact is an LLM benchmark? | Track taxonomy and scope boundaries |
| 2 | What stages does a benchmark go through from idea to retirement? | Stage-1 build plan |
| 3 | How are items authored, validated, and gold-labelled? | Tracks A and D item-writing protocol |
| 4 | What metrics actually track quality vs. just look quantitative? | Scoring functions per track |
| 5 | How do you make eval results statistically defensible? | Sample sizes, repetitions, confidence intervals |
| 6 | What tools and frameworks should a builder pick from? | Technical stack |
| 7 | How is a benchmark documented for reviewers, regulators, and users? | Eval card, data card, governance |
| 8 | What have adjacent domains (medical, legal, multilingual, safety) already learned? | Patterns to copy and traps to avoid |
| 9 | Given all the above, what does Stage 1 actually look like? | Concrete 90-day blueprint |

The chapters that follow take these in order. Section 29 is the synthesis – if a reader only reads two chapters, [Chapter 22](#chapter-22--the-eight-stage-benchmark-lifecycle) and [Chapter 29](#chapter-29--athlon-eval-stage-1-implementation-blueprint) are the most load-bearing.

---

## Chapter 21 – What an LLM benchmark is, and isn't

### 21.1 A working definition

An **LLM benchmark** is a frozen, versioned, publicly identifiable collection of inputs paired with a scoring procedure, designed to compare how different language models perform on a defined construct. The four words doing work in that definition are:

- **Frozen** – the same items, in the same order, with the same prompts, every time. A benchmark whose contents drift over time is a dataset, not a benchmark.
- **Versioned** – when items change, the version number bumps and historical results are tagged with the version they used. EleutherAI's lm-evaluation-harness explicitly supports per-task version numbers for this reason **[verified, source 8]**.
- **Scoring procedure** – it is not enough to publish inputs; the rules for how raw model outputs convert to a score must be reproducible end-to-end.
- **Construct** – the benchmark is supposed to measure *something specific*. Construct validity is the academic name for "your benchmark actually measures what you claim it measures". Almost every benchmark failure mode reduces to a construct validity failure.

### 21.2 Taxonomy: capability vs alignment vs safety vs operational

Modern LLM benchmarks split along four orthogonal axes:

| Axis | Question it asks | Examples | Athlon Eval track(s) |
|---|---|---|---|
| **Capability** | What does the model *know* or *do*? | MMLU, MMLU-Pro, BBH, BBEH, SWE-Bench, GPQA | A, B |
| **Alignment** | Does the model follow instructions, stay on task, sound right? | MT-Bench, Arena-Hard, AlpacaEval 2.0 | C, E, G |
| **Safety** | Does the model refuse what it should refuse? | HarmBench, AgentHarm, JailbreakBench | F |
| **Operational** | Does the model do useful work in a deployed pipeline? | SWE-Bench Verified, τ-Bench, RAG benchmarks | D, and elements of B/C/E/G |

A clean Athlon Eval design slots every track into exactly one axis. Mixing axes inside a single track is one of the most common reasons academic benchmarks fail to convince operators – the score becomes hard to interpret because nobody knows what it actually measured.

### 21.3 Closed-form vs generative scoring

Within any axis, scoring can be **closed-form** (the model's output is checked against a single correct answer) or **generative** (the model's output is graded by a richer judge against a rubric).

| Type | Best for | Failure mode |
|---|---|---|
| Closed-form (multiple choice, exact match, F1, pass@k) | Knowledge, code, structured extraction | Vulnerable to format-juggling and contamination |
| Generative (rubric, LLM-as-judge, human eval) | Open coaching dialogue, plan generation, retention drafts | Vulnerable to judge bias and rater drift |

For Athlon Eval, Track A is closed-form (MCQ + short-answer), Track D is mixed (deflection accuracy is closed-form, escalation quality is generative), and Tracks B/C/E/G are generative. This is also how the literature is split – HELM, MMLU, BBH, MedQA are closed-form; MT-Bench, Arena-Hard, AlpacaEval, MedHELM clinical scenarios are generative **[verified, sources 2, 4, 6, 14]**.

### 21.4 Static vs dynamic vs live

A third axis the 2024–2025 literature has made unavoidable:

- **Static** – items frozen at release time. Examples: MMLU, BIG-Bench. Saturate fast and contaminate fast.
- **Dynamic** – items refreshed on a schedule. Example: LiveCodeBench, which adds new programming-contest problems monthly and tags every problem with a release date so a model can be scored only on items released after its training cutoff **[verified, source 22]**.
- **Live** – items generated by users in real time. Example: LMArena (formerly Chatbot Arena), where users compare two anonymous models on prompts they wrote themselves **[verified, source 13]**.

Athlon Eval Stage 1 should be **mostly static** (because reproducibility for academic credibility requires it) with one **dynamic** element: a held-out monthly refresh of 5-10 % of Track D's club FAQs, generated synthetically and never released in the public bundle. This gives contamination resistance without complicating the headline reproducibility story. A live track is out of scope until Stage 3 because operating it requires full-time community moderation.

### 21.5 What an LLM benchmark is *not*

These distinctions are worth keeping clear because the word "benchmark" gets stretched in industry contexts to mean things it isn't:

| Not a benchmark | Why |
|---|---|
| A leaderboard | Leaderboards display benchmark results; they are not the benchmark itself. |
| A vendor's internal eval suite | Not frozen, not public, not versioned, not peer-reviewed. |
| A synthetic eval generator | A generator can build benchmarks; it isn't one. |
| A real-time monitoring dashboard | Production monitoring measures *deployed* model behaviour, not *intrinsic* model capability under controlled conditions. |
| A red-team exercise | A point-in-time adversarial exploration; useful but not reproducible. |

Athlon Eval must be the first thing on this list (a real benchmark) while interoperating with the others (it should ship a leaderboard, plug into vendor CI, and be re-runnable inside a monitoring dashboard).

---

## Chapter 22 – The eight-stage benchmark lifecycle

This is a synthesis of how the major frameworks describe their own benchmark-creation processes – it draws on HELM's published methodology **[verified, source 2]**, the lm-evaluation-harness contributor docs **[verified, source 8]**, and the NeurIPS Datasets and Benchmarks track guidelines **[verified, source 4]**. None of those sources publishes an explicit "eight-stage lifecycle" – the staging here is the author's reorganisation **[inferred]**. Athlon Eval Stage 1 runs through stages 1–7; stage 8 is permanent.

### Stage 1 – Define the construct

The single most important step. Every later decision flows from "what exactly are we measuring?". This is where the construct must be:

- **Operationalisable** – there has to be a concrete observable that proxies for it.
- **Decomposable** – it must split into sub-constructs the items can target.
- **Distinguishable from adjacent constructs** – Track A "exercise-science knowledge" must not silently collapse into Track C "coaching dialogue quality".

For Athlon Eval, every track gets a one-paragraph construct definition that survives the entire lifecycle and is referenced in the eval card. Track F's construct, for example, is *"refuses or escalates any prompt that meets the safety taxonomy in Appendix F1, irrespective of phrasing, language, role-play framing, or multi-turn ramp-up, while still completing benign prompts that share surface features with unsafe ones"*.

### Stage 2 – Source or author the items

Three patterns dominate the literature, plus one that is becoming more common:

1. **Real-world artefacts** – exam questions (M3Exam **[verified, source 17]**), USMLE for MedQA **[verified, source 14]**, programming contests for LiveCodeBench **[verified, source 22]**, Stack Exchange for many Q&A benchmarks. *Strength*: high ecological validity. *Weakness*: licensing and contamination risk.
2. **Pure expert authoring** – LegalBench's 162 tasks were written by 40 lawyers and legal scholars **[verified, source 18]**; SportQA Level 3 was written by domain coaches **[verified, Part I §1.1]**. *Strength*: covers gaps real artefacts miss. *Weakness*: expensive and slow.
3. **Templated + expert review** – SportQA Levels 1 and 2 used semantic distractor libraries that were then edited by 36 student-athletes **[verified, Part I §1.1]**. *Strength*: scales fast. *Weakness*: semantic monotony if templates are too rigid.
4. **LLM-assisted with expert filtering** – an LLM drafts items, a human accepts/rejects/edits. Increasingly common since 2024. *Strength*: cheap volume. *Weakness*: imports the source LLM's blind spots into the benchmark.

For Stage 1, Track A should use pattern 3 (template + expert review of every item by a coach with at least L4-equivalent qualifications). Track D should use pattern 4 (synthetic club-ops corpus + expert filtering). Track F should use pattern 1 + 2 hybrid (real medical contraindications + expert-authored adversarial prompts). This is a deliberate inversion of "always use real data" – the safety track is the one where authored adversarial items matter most, because the real distribution is too sparse and too unevenly licensable.

### Stage 3 – Validate items with experts

This is where most benchmarks cut corners. The 2024 MMLU audit found **6.5 % of items had ground-truth errors**, with the Virology subset at **57 %** wrong-or-ambiguous **[verified, source 6]**. That is a benchmark where the ceiling is mathematically below 100 %, and it's the most-cited benchmark in the field. The lesson: validation cost is real, and skipping it permanently corrupts the artefact.

Validation has two parts:

- **Per-item review** – does a domain expert agree this is the correct answer? Two independent experts is the minimum; three is the academic standard for medical-grade work **[inferred from sources 14, 19]**.
- **Inter-annotator agreement (IAA) reporting** – the benchmark must publish how much its annotators agreed. Two metrics dominate:

| Metric | Use when | Target |
|---|---|---|
| **Cohen's kappa** | Exactly 2 annotators, same items | ≥0.61 substantial, ≥0.81 almost perfect (Landis & Koch 1977) **[verified, source 26]** |
| **Krippendorff's alpha** | ≥3 annotators, possibly missing data | Same thresholds as kappa **[verified, source 26]** |

The RIFT rubric-failure-mode taxonomy paper reports an average kappa of 0.64 and alpha of 0.60 across rubric annotation tasks **[inferred from source 5; the figures appear in the OpenReview PDF abstract but were not independently re-verified against the full PDF]**. That is the realistic floor for a complex generative-eval rubric – simpler closed-form items should be higher.

Athlon Eval should target **kappa ≥0.7 on Track A** and **alpha ≥0.6 on Tracks D and F**, both reported per sub-track in the eval card.

### Stage 4 – Pilot test

Before the benchmark is frozen, it gets run end-to-end on 2–4 baseline models:

- One frontier closed-source model (GPT-class)
- One frontier closed-source model from a different vendor (Claude- or Gemini-class)
- One open-weight frontier model (Llama- or DeepSeek-class)
- Optionally, one small open model (≤8B) to anchor the difficulty floor

The pilot answers four questions that decide whether the benchmark goes to release:

1. **Difficulty distribution** – is anything 0 % or 100 % across all baselines? Items at the floor or the ceiling carry no information and should be cut or rewritten.
2. **Variance** – on the generative items, how reproducible is the score across 5 reruns at the same temperature? If variance >10 % of the mean, the rubric or the prompt is underspecified.
3. **Format pathologies** – are any models failing because of prompt format rather than content? If GPT-4 scores 90 % and Llama 3 scores 20 % only because Llama emits a different bullet style, the harness is broken, not the model.
4. **Surprises that change the construct** – if the baselines all do well on what was meant to be a hard sub-track, the construct definition needs revisiting.

Athlon Eval Stage 1 should pilot on at least **5 closed + 5 open** models. This matches the floor used by HELM (30 models on launch) **[verified, source 2]** and is enough to detect format pathologies. The pilot takes ~2 weeks and is the gate before "freeze and publish".

### Stage 5 – Build the harness

The benchmark itself is items + scoring rules. The harness is the code that runs models against those items. Stage 6 covers the framework choice in depth; the principle here is that the harness is the part that gets versioned independently from the items, because bug fixes in scoring are common while item changes are rare.

### Stage 6 – Run baselines and freeze

The same matrix as Stage 4, expanded. Every score gets:
- A point estimate
- A confidence interval (see [Chapter 25](#chapter-25--statistical-rigor-and-reproducibility))
- A cost figure (USD per 1k items at benchmark-time pricing)
- A latency figure (p50 and p95)
- A reproducibility figure (variance across ≥5 runs)

The frozen artefact is then tagged `v1.0.0` and never edited.

### Stage 7 – Publish, leaderboard, paper

Best practice as of 2026:

- Items + harness + baselines on GitHub (MIT or Apache 2.0)
- Dataset on Hugging Face Hub with **Croissant metadata** (now mandatory for NeurIPS Datasets and Benchmarks track) **[verified, source 4]**
- Leaderboard on a project website
- Paper on arXiv, then submitted to NeurIPS D&B / ICML datasets / ACL findings
- Reproducibility appendix with seeds, prompts, decoding parameters, hardware

### Stage 8 – Maintain and retire

A benchmark is not done when it ships. It needs:

- **Contamination monitoring** – when a new frontier model releases, test whether its score is implausibly high relative to its cutoff (the LiveCodeBench technique **[verified, source 22]**)
- **Refresh** – periodic addition of new items to the held-out fold
- **Retirement criteria** – published in advance: e.g. "Athlon Eval v1 retires when 3 frontier models all score >95 % on Track A"
- **Backwards compatibility** – v1 results stay valid forever; v2 launches alongside, doesn't replace

This is where most benchmarks fail. MMLU saturated and was patched into MMLU-Pro **[verified, source 3]**; BIG-Bench saturated and was patched into BBEH **[verified, source 1]**; HELM has remained healthier mainly because it was modular from day one and could absorb new sub-benchmarks. Athlon Eval should plan retirement at v1 launch.

---

## Chapter 23 – Construction methodology

### 23.1 Item authoring in detail

Each authoring pattern from §22 has a specific protocol the literature converges on. The four protocols, in order of how Athlon Eval Stage 1 will use them:

**Protocol A – template + expert review (Track A primary).** Steps:
1. Source ~10 templates per sub-domain (e.g. "FITT principle for [population] doing [modality]").
2. Generate distractors from a structured library (e.g. wrong-population, wrong-volume, wrong-progression, plausible-but-contraindicated).
3. Run each item past two coaches independently; resolve disagreements with a third.
4. Discard any item where both coaches needed >2 minutes to answer (it's testing trivia, not knowledge).
5. Discard any item where the expected answer changed across the two coaches (construct ambiguous).
6. Track per-item provenance (template ID, distractor source, reviewers).

Yields ~60 % of generated items as keepers in published comparable work **[inferred from sources 5, 14, 18]**.

**Protocol B – synthetic corpus + expert filtering (Track D primary).** Used for the club-ops knowledge base:
1. Define a fictional gym chain in a one-page brief (brand, locations, pricing, cancellation policy, equipment, classes, staff structure).
2. Have an LLM generate ~500 plausible member-support documents (FAQ entries, policy docs, class descriptions, billing edge cases).
3. Have an LLM generate ~1500 member queries against that corpus, half of which are answerable from the corpus and half not.
4. Have a club operator (real, recruited from Qodeca's network) review ~10 % of queries for plausibility and adjust the answerable / not-answerable ratio.
5. Mark each query with its expected outcome (answer / clarify / escalate / refuse / decline-out-of-scope).

This is the same pattern HELM used for some scenario construction **[verified, source 2]** and that Inspect AI evals like SWE-Bench Verified use for filtering **[verified, source 25]**.

**Protocol C – pure expert authoring (Track F primary).** For safety items, scale matters less than coverage of the harm taxonomy:
1. Build the harm taxonomy first (Appendix F1 in the eval card).
2. Have an exercise physiologist write 5–10 prompts per harm class.
3. Have a clinician review each prompt for medical accuracy.
4. Run each prompt past 2 baseline models to confirm the *expected* refusal/escalation actually fails on at least one baseline (otherwise the item is too easy and adds no signal).

LegalBench used a flavour of this with 40 lawyers contributing 162 tasks **[verified, source 18]**. AgentHarm used 110 unique behaviours plus 330 augmented variants across 11 harm categories **[verified, source 21]**. Athlon Eval Track F should target ~150 unique safety prompts in seven languages, which is in the same order of magnitude.

**Protocol D – LLM-assisted at scale (auxiliary, all tracks).** For paraphrasing, translation, and adversarial variant generation only. Never as a primary authoring method.

### 23.2 Gold answers and rubrics

For closed-form items, "gold answer" means literally the correct option and a list of accepted equivalent forms (whitespace, capitalisation, synonyms). For generative items, a rubric replaces the gold answer.

A good rubric has four parts:

1. **Anchored scale** – not "1 to 5" but "1 = factually wrong, 3 = correct but unsafe phrasing, 5 = correct, safe, contextual". The anchors are the rubric.
2. **Independent dimensions** – do not score one dimension as a side effect of another. MT-Bench scores helpfulness, relevance, accuracy, depth, creativity, level-of-detail as separate axes **[verified, source 9]**.
3. **Examples** – at least one calibration example per anchor point.
4. **Failure modes catalogue** – the RIFT paper's contribution: a published list of every way a rubric has been seen to fail in practice (e.g. graders confuse fluency for accuracy), so reviewers can self-check **[verified, source 5]**.

Athlon Eval Tracks B, C, E, G all use rubric scoring; Track A is closed-form; Track D is mixed (deflection accuracy is closed-form, escalation reasoning is rubric); Track F is closed-form (refused yes/no plus did-it-explain-the-reason).

### 23.3 Inter-annotator agreement in practice

Four practical rules:

1. **Compute IAA on a held-out validation slice, not on the full set.** Annotating the entire benchmark twice is wasteful. ~10 % is enough to detect rubric problems **[inferred from source 5]**.
2. **Report per-sub-track, not just overall.** A high overall kappa can hide a low-kappa subset that's the source of all the noise.
3. **If kappa is below 0.6, the rubric is broken, not the annotators.** Rewrite the rubric and re-annotate. Bumping the agreement target by adding annotators is not the fix.
4. **Never use only pairwise percent agreement.** It looks high (~85 %) when chance agreement is also high (e.g. on a binary classification with 80/20 base rates). Cohen's kappa corrects for chance; pairwise percent does not **[verified, source 5]**.

### 23.4 Pilot testing and difficulty calibration

The pilot is where the benchmark either becomes useful or becomes a leaderboard prop. Three things to look for that don't get enough attention in the literature:

- **Item discrimination** – does the item actually distinguish stronger models from weaker ones? An item that everyone gets right or everyone gets wrong has zero discrimination. Item Response Theory (the same statistical machinery used for educational testing) is starting to be applied to LLM benchmarks but is still rare. For Athlon Eval, the pragmatic substitute is: rank baselines on each item, drop any item where the rank correlation across reruns is <0.5.
- **Format sensitivity** – swap the prompt format (e.g. "Question:" → "Q:") and re-run. If scores move >2 percentage points, the benchmark is measuring format following rather than the construct.
- **Language sensitivity** – on multilingual tracks (every Athlon Eval track), translate a 10 % slice into all target languages and check that the same items are easier/harder in each language. Systematic differences point to translation artefacts, not model differences. M3Exam handles this by sourcing items in their original language rather than translating **[verified, source 17]**, which is the gold standard.

---

## Chapter 24 – Evaluation metrics and scoring

### 24.1 Closed-form metrics

| Metric | Formula in plain English | Best for | Watch out for |
|---|---|---|---|
| **Accuracy** | Items right / items total | MCQ tracks | Hides per-class imbalance |
| **Exact match (EM)** | Output equals gold string | Short-answer factoid | Sensitive to whitespace, casing |
| **F1 (token)** | Harmonic mean of precision and recall over output tokens | Span extraction | Rewards verbosity |
| **pass@k** | Probability ≥1 of k samples passes tests | Code generation | Requires expensive sampling at high k |
| **ECE (expected calibration error)** | Gap between confidence and accuracy | Calibrated knowledge tasks | Needs reliable probability outputs |
| **Brier score** | Mean squared probability error | Probabilistic forecasting | Bounded but not directly interpretable |

Track A and the closed-form portion of Track D use accuracy as the primary metric and ECE as a secondary metric. ECE matters specifically because Athlon Eval is meant to inform vendor selection – a model that knows when it doesn't know is more useful in a club concierge than a model with the same accuracy but worse calibration.

### 24.2 Why BLEU, ROUGE, and BERTScore are not enough

The 2010s NLP toolkit – BLEU for translation, ROUGE for summarisation, BERTScore for everything else – does not work for modern LLM evaluation. The case against, distilled:

- **BLEU and ROUGE are surface-level n-gram overlap.** They reward "the same words in the same order" and punish paraphrase. For free-form coaching answers, this is exactly the wrong incentive **[verified, source 11]**.
- **Both are insensitive to word order beyond the n-gram window.** "Always avoid the leg press" and "leg press: always avoid" are different in meaning to a coach but identical to ROUGE **[verified, source 11]**.
- **BERTScore is contextual but still token-level.** It correlates better with human judgment than BLEU/ROUGE but still misses holistic properties – reasoning depth, safety, tone, factuality – that matter most in coaching **[verified, source 11]**.
- **Language coverage is uneven.** BERTScore depends on the underlying BERT model; non-English performance is not guaranteed and the default English model is 1.4 GB on disk **[verified, source 11]**.

Practical conclusion: do not use BLEU, ROUGE, or BERTScore as a *headline* metric for any Athlon Eval generative track. They can be reported as supplementary numbers if reviewers ask, but the headline scoring for Tracks B, C, E, G is rubric-based with LLM-as-judge plus a human spot-check.

### 24.3 LLM-as-judge

The dominant generative-eval method since 2023, introduced by Zheng et al. in the MT-Bench paper **[verified, source 9]**. Three modes:

| Mode | What the judge sees | Output |
|---|---|---|
| **Single, no reference** | One model response + rubric | Score on rubric scale |
| **Single, with reference** | One model response + rubric + a reference answer | Score on rubric scale |
| **Pairwise** | Two model responses + rubric | A wins / B wins / tie |

Pairwise is the most reliable mode (judges are better at "which is better" than at "how good is this on a scale of 1–10") and is what MT-Bench, Arena-Hard, and AlpacaEval 2.0 use **[verified, source 9]**. For Athlon Eval, every generative track scores **pairwise against a strong reference model**, with the judge prompted to apply the rubric step-by-step (G-Eval style chain-of-thought) **[verified, source 10]**.

Reported agreement rates between strong LLM judges and human experts hit ~80 % in the original MT-Bench paper, which is in the same range as human-human agreement **[verified, source 9]**. That number is the floor, not the ceiling – it means LLM-as-judge is acceptable as a primary metric *when complemented by human spot-check*, never as the sole metric.

### 24.4 Judge biases – and how to defuse them

Five biases are now well-documented in the 2024–2025 literature **[verified, source 7]**:

| Bias | What it does | Defuse |
|---|---|---|
| **Position bias** | Judge prefers the response in a specific slot (often A) regardless of quality | Run every pair twice with order swapped; require both runs to agree before counting the win |
| **Verbosity bias** | Judge rewards longer responses even when shorter is better | Either truncate both responses to a length cap, or include a "concision" axis in the rubric |
| **Self-preference bias** | Judge prefers responses generated by its own family. GPT-4 favours itself by ~10 % win rate; Claude-v1 by ~25 % **[verified, source 9]** | Use a different model family as judge than any model being scored. If unavoidable, ensemble across ≥2 judge families |
| **Style/formality bias** | Judge confuses fluency or formality for correctness | Anchor the rubric on factual claims rather than tone |
| **Length-pretrained bias** | Judges trained with RLHF favour the formats they were optimised on | Calibrate against human raters on a held-out slice |

For Athlon Eval, the mitigation stack is fixed: pairwise + order swap + reference answer + chain-of-thought rubric application + judge ensemble (Claude family + GPT family + at least one open-weight strong judge). This adds compute but it is the only configuration that survives reviewer scrutiny in 2026.

### 24.5 Human evaluation

Human evaluation is still the gold standard the LLM judge is benchmarked against. The Athlon Eval design borrows from the medical-LLM literature, which has the most mature human-eval protocols:

- **Crowd vs expert** – crowd raters work for low-stakes tasks (general dialogue quality) but fail on domain-specific safety. Expert raters are mandatory for Track F and strongly recommended for Tracks B, C, E **[inferred from sources 14, 19]**.
- **Pairwise vs Likert** – pairwise is faster, more reliable, and cheaper. Use pairwise wherever possible.
- **Number of raters per item** – three is the minimum for medical-grade work, with majority vote or Krippendorff's alpha as the aggregation rule **[inferred from sources 5, 14]**.
- **Sample size** – human eval is the budget bottleneck. ~150 items per track, 3 raters each, is realistic for a Stage-1 launch and matches what most academic benchmarks publish.
- **Cost** – plan ~USD 10–25 per item for fitness experts at 2026 rates, ~USD 5 per item for crowd raters where applicable.

The human-eval slice is published alongside the LLM-judge scores so that anyone reproducing the benchmark can verify the LLM judge is still tracking human judgment six months later.

### 24.6 Pairwise leaderboards (Bradley-Terry)

The Chatbot Arena methodology paper (Chiang et al. 2024, arXiv 2403.04132) **[verified, source 13]** documents the modern best practice for converting pairwise comparisons into a single per-model number:

- Fit a **Bradley-Terry model** (the maximum likelihood version of Elo, treating model strength as fixed and pairwise outcomes as observations).
- Compute **bootstrap confidence intervals** on the resulting strength estimates.
- Report both the point estimate and the interval.
- Recompute from scratch every update; do not use online Elo updates that depend on pair ordering.

This is what the LMArena leaderboard switched to in late 2023 **[verified, source 13]**, and it is what Athlon Eval Tracks C and G should use for the headline ranking. Tracks B and E use rubric-based scoring rather than pairwise, because the construct is better-defined.

### 24.7 Athlon Eval implications

Concretely, the metric stack for Stage 1:

| Track | Primary metric | Confidence interval method | Secondary metrics |
|---|---|---|---|
| A (Knowledge) | Accuracy on MCQ | Bayesian (Bowyer 2025) | ECE, per-domain breakdown |
| D (Operational) | Closed-form deflection accuracy + rubric on escalation | Bayesian + bootstrap | Cost, latency, refusal rate |
| F (Safety) | Refusal accuracy on adversarial set + benign-precision | Bayesian | False-refusal rate, jailbreak robustness |

Stage 1 explicitly does not use BLEU/ROUGE/BERTScore at all.

---

## Chapter 25 – Statistical rigor and reproducibility

### 25.1 The "few hundred" rule of thumb

The Bowyer et al. ICML 2025 spotlight position paper *Don't Use the CLT in LLM Evals With Fewer Than a Few Hundred Datapoints* (arXiv 2503.01747) is the load-bearing 2025 statistical contribution **[verified, source 15]**. The headline:

> CLT-based confidence intervals are appropriate when benchmarks have thousands of items. Below a few hundred items they routinely under-estimate uncertainty – sometimes by enough to flip a "model A beats model B" claim.

The authors recommend Bayesian credible intervals or specific frequentist methods (Wilson, Clopper-Pearson for binomial proportions) instead of CLT-based standard errors. Bootstrap intervals are also flagged as poor in the small-sample regime **[verified, source 15]**.

### 25.2 Confidence intervals when you do have thousands of items

Miller's 2024 paper *Adding Error Bars to Evals* (arXiv 2411.00640) **[verified, source 16]** gives the canonical recipe for the large-sample regime. The four recommendations:

1. **Compute standard errors via the CLT** – `SE = σ / √n`, where σ is the per-item standard deviation.
2. **Use clustered standard errors when items come in groups.** Ignoring clustering can under-estimate the SE by up to 3× in real benchmarks **[verified, source 16]**.
3. **Reduce variance by resampling answers and analysing next-token probabilities** rather than discrete completions where possible.
4. **For pairwise model comparisons, do statistical inference on question-level paired differences**, not population-level summary statistics.

For Athlon Eval, Miller's recipe applies to Track A (which will have ~1500–2500 items per language) and Bowyer's recipe applies to Tracks D and F (which will have ~150–500 items per language). Both methods coexist; the eval card declares which method was used per track.

### 25.3 Variance across runs

Modern LLMs are not deterministic by default. The same prompt at temperature >0 returns different outputs. The same prompt at temperature 0 *should* return the same output but in practice doesn't, because:

- Vendor APIs can route to different model snapshots transparently.
- Tokeniser non-determinism can flip last-token sampling.
- Distributed inference can introduce floating-point ordering variance.

The Hypertrophy-plan reproducibility paper (PMC11963122, see Part I §4) showed frontier models produce materially different exercise plans across reruns of identical prompts **[verified, Part I §4]**. The Athlon Eval reproducibility rule, lifted directly from that finding:

- **Run every scenario ≥5 times.**
- **Report the mean, the per-run variance, and the across-run min/max.**
- **A model with high variance is penalised on a separate "reproducibility" axis** even if its mean score is high.

This is non-negotiable for a coaching benchmark. A program-generation LLM that gives a different plan every time is effectively unusable in production regardless of its average quality.

### 25.4 Paired comparisons

When comparing model A and model B, the right test is on the per-item difference `score_A(i) − score_B(i)`, not on the means `mean(score_A) − mean(score_B)`. Paired tests are more powerful because they cancel out item-level noise. Miller spells this out explicitly **[verified, source 16]**, and it is what every published 2024+ benchmark with statistical rigor does. Athlon Eval pairwise rankings use paired tests by default.

### 25.5 Contamination and data leakage

The single biggest threat to benchmark validity in 2026. Three layered defences:

**Defence 1 – Detection.** Check whether a candidate model has seen the items.

| Method | How it works | Reliability |
|---|---|---|
| **N-gram overlap (13-gram, GPT-3 default)** | Search for benchmark item substrings in the training corpus | High when training data is open; useless for closed models |
| **Perplexity-based** | Contaminated items have lower per-token perplexity than novel items | ~40–50 % accuracy in independent studies; **unreliable on its own** **[inferred from source 12; the qualitative claim is well-established but the specific 40–50 % figure should be re-verified against the survey PDF before external citation]** |
| **Min-k%** | Membership inference via low-probability token analysis | Mixed, similar reliability to perplexity |
| **Time-segmented evaluation** | Score only on items released after the model's training cutoff | High when item release dates are tracked **[verified, source 22]** |

The honest summary: **detection is not solved.** A 2024 survey of detection methods reports that perplexity, n-gram, and min-k% all fail to reliably distinguish contaminated from uncontaminated items in held-out studies **[verified, source 12]**.

**Defence 2 – Decontamination.** When training a model, scrub the training corpus of known benchmark items. 13-gram overlap is the standard rule (GPT-3 used it; many later models adopted it) **[verified, source 12]**. But the HumanEval benchmark has been measured to overlap RedPajama-Data-1T and StarCoder-Data at **8–18 %** *despite* decontamination efforts **[verified, source 27]**. Decontamination is best-effort.

**Defence 3 – Construction-time resistance.** This is where Athlon Eval should focus, because it is the only defence the benchmark builder fully controls.

- **Held-out fold** – release N items publicly (the "open" benchmark) and keep M items private (the "evaluation" benchmark). Never publish the private fold. Vendors send model outputs through Athlon Eval's hosted scoring; the benchmark team holds the answer key.
- **Rotation** – periodically refresh the held-out fold (~10 % per quarter) so a leak in month 1 is repaired by month 4.
- **Synthetic-when-possible** – Track D's club-ops corpus is synthetic, so it cannot be in any pre-training set by construction (assuming no leaked release).
- **Time-tagged items** – every item carries an authoring date; results are reported per cutoff window.

This combination is roughly what LiveCodeBench does for code **[verified, source 22]**, and is the most defensible architecture currently known.

### 25.6 Reproducibility checklist for Athlon Eval Stage 1

The following must be in the eval card and the GitHub release:

- [ ] Random seeds for every step (item shuffling, sampling, judge prompts)
- [ ] Decoding parameters per model (temperature, top-p, max tokens)
- [ ] Exact prompt template per task
- [ ] Tokeniser used per scoring step
- [ ] Hardware profile (so latency numbers are interpretable)
- [ ] Framework version (lm-eval-harness, Inspect, etc.)
- [ ] Model snapshot identifiers (date or commit hash)
- [ ] Human-eval rater pool description
- [ ] Number of reruns
- [ ] Per-rerun raw scores in a CSV alongside the leaderboard

Anyone with the same model access can re-run Stage 1 from this list and reproduce the leaderboard within published intervals.

---

## Chapter 26 – Tools and frameworks landscape

### 26.1 Decision framework

There is no single best framework. The choice depends on what mix of capabilities the benchmark needs. For Athlon Eval Stage 1, the requirements are:

| Requirement | Why |
|---|---|
| Multi-provider model adapters (closed + open-weight) | Vendor neutrality is a design pillar |
| YAML or declarative task config | Lower the bar for community contributions |
| Sandboxed execution | Track F includes prompts that may exercise unsafe tool calls |
| Multi-turn dialogue support | Track C is multi-turn |
| Pluggable scoring (closed-form + LLM judge + human) | Different tracks need different scoring |
| Cost and latency reporting | Operator-grade benchmark requirement |
| Reproducibility primitives (seeding, logging) | Every cell in §25.6 |
| Active maintenance | Avoid abandoned projects |
| Permissive licence | Open-core publication |

### 26.2 lm-evaluation-harness (EleutherAI)

The de facto standard for academic LLM evaluation. Used by NVIDIA, Cohere, BigScience, Mosaic ML, and as the backend for the Hugging Face Open LLM Leaderboard **[verified, source 8]**.

| Aspect | Detail |
|---|---|
| **Language** | Python |
| **Licence** | MIT |
| **Task definition** | YAML config with `doc_to_text`, `doc_to_target`, `doc_to_choice` |
| **Versioning** | Per-task version numbers; bump on breaking change |
| **Strength** | Massive task library (200+), well-trodden, easy CI integration |
| **Weakness** | Designed for closed-form tasks; multi-turn and agent support are limited |
| **Best for Athlon Eval** | Track A primary harness |

Adding a Track A sub-task is a YAML edit + test commit – this is the lowest-friction path for community contributions to Athlon Eval. **[verified, source 8]**

### 26.3 HELM (Stanford CRFM)

The original "holistic" framework. The 2022 paper introduced the 7-metric × 16-scenario matrix and tested 30 models with 96 % coverage **[verified, source 2]**.

| Aspect | Detail |
|---|---|
| **Language** | Python |
| **Licence** | Apache 2.0 |
| **Task definition** | Python scenarios + structured metrics |
| **Strength** | Multi-metric reporting (accuracy, calibration, robustness, fairness, bias, toxicity, efficiency) |
| **Weakness** | Higher contribution friction than lm-eval-harness; opinionated structure |
| **Best for Athlon Eval** | Methodological influence on the eval card; not necessarily the runtime |

HELM's biggest contribution to Athlon Eval is the *reporting taxonomy* – every track in Athlon Eval should publish all 7 HELM-style metrics where applicable, even if the headline score is just one of them. This is how we make the benchmark useful to operators who care about robustness and cost as much as accuracy.

### 26.4 Inspect AI (UK AISI)

The most modern of the open frameworks. Built by the UK AI Security Institute as the platform for their own safety and capability evals **[verified, source 25]**.

| Aspect | Detail |
|---|---|
| **Language** | Python |
| **Licence** | MIT |
| **Architecture** | `dataset → Task → Solver → Scorer` primitives |
| **Multi-provider** | OpenAI, Anthropic, Google, Groq, Mistral, xAI, AWS Bedrock, Azure, Together, Cloudflare, Goodfire, plus local vLLM/Ollama/llama.cpp |
| **Agent support** | Built-in agents, multi-agent primitives, can call external agents (Claude Code, Codex CLI, Gemini CLI) |
| **Sandbox** | Docker out of the box; Kubernetes / Proxmox via extension |
| **UI** | Web Inspect View + VS Code extension |
| **Pre-built evals** | 100+ ready to run **[verified, source 25]** |
| **Best for Athlon Eval** | Tracks D, F, and (later) C – everything that needs sandboxing or multi-turn |

Inspect is the recommendation for Athlon Eval's main runtime. It is the only framework whose primitives map cleanly onto what Stage 1 actually needs (sandboxed safety prompts, multi-turn member dialogue, multi-provider parity, structured logging). Its async architecture also makes it the fastest at full-throughput evaluation – if a vendor accepts 100 concurrent connections, Inspect uses all 100 **[verified, source 25]**.

### 26.5 DeepEval (Confident AI)

Open-source framework with a commercial cloud companion. Pytest-like ergonomics **[verified, source 10]**.

| Aspect | Detail |
|---|---|
| **Language** | Python |
| **Licence** | Apache 2.0 |
| **Strength** | 50+ ready-made metrics (G-Eval, faithfulness, hallucination, RAG-specific, agent-specific, chatbot-specific) |
| **Weakness** | Designed for application testing rather than model benchmarking; less academic acceptance |
| **Best for Athlon Eval** | Internal Qodeca delivery QA, not the public benchmark itself |

DeepEval is the right tool for Qodeca's *delivery* use case (CI gate on every Qodeca-built feature) and the wrong tool for the *publishing* use case. Athlon Eval should use Inspect for the public benchmark and recommend DeepEval as a downstream integration for client-facing CI.

### 26.6 Promptfoo

CLI-first, YAML-driven, multi-provider. Acquired by OpenAI in March 2026 **[verified, source 11]**.

| Aspect | Detail |
|---|---|
| **Language** | TypeScript / JavaScript |
| **Licence** | MIT |
| **Strength** | Single-file YAML test definitions; multi-provider matrix view; built-in red-teaming / pentest capabilities; CI/CD-friendly |
| **Weakness** | Less academic standing; closed-form bias |
| **Best for Athlon Eval** | A useful adjunct for Track F red-teaming; not the primary harness |

### 26.7 OpenAI Evals

The original open-source eval framework from OpenAI. Largely superseded for cross-provider work by lm-eval-harness, Inspect, and Promptfoo. Historical note: many of the patterns in newer frameworks (YAML configs, declarative assertions) trace back to OpenAI Evals.

### 26.8 RAGAS

Reference-free RAG evaluation. Faithfulness, answer relevancy, context precision, context recall **[verified, source 28]**.

| Aspect | Detail |
|---|---|
| **Language** | Python |
| **Licence** | Apache 2.0 |
| **Strength** | Reference-free (no ground truth required), Hugging Face Datasets integration |
| **Weakness** | Narrowly focused on RAG; overlap with DeepEval |
| **Best for Athlon Eval** | Track D scoring of grounded-answer quality, alongside Inspect's primary harness |

### 26.9 Comparison summary

| Framework | Best for | Use in Athlon Eval Stage 1 |
|---|---|---|
| **lm-evaluation-harness** | Closed-form, MCQ, knowledge | **Track A primary** |
| **Inspect AI** | Multi-provider, sandboxed, agentic, multi-turn | **Tracks D + F primary; main runtime** |
| **HELM** | Multi-metric reporting taxonomy | Methodological reference for the eval card |
| **DeepEval** | Pytest-like app-level eval | Recommend for client CI; not for the public benchmark |
| **Promptfoo** | YAML-first, red-team adjunct | Track F red-team adjunct |
| **OpenAI Evals** | Historical | Patterns to borrow; not a runtime |
| **RAGAS** | Grounded-answer scoring | Track D auxiliary metric |

### 26.10 Stack recommendation

For Athlon Eval Stage 1, the stack is:

- **Primary runtime**: Inspect AI for everything except Track A
- **Track A runtime**: lm-evaluation-harness (because of community familiarity and easier external contribution)
- **Reporting taxonomy**: HELM-style 7 metrics
- **Auxiliary**: RAGAS for Track D grounded-answer scoring; Promptfoo for Track F red-team variants
- **Hosting**: GitHub for code, Hugging Face Datasets for items, project website for leaderboard
- **CI/CD**: GitHub Actions running Inspect against a pinned model snapshot weekly

This stack is intentionally boring. Every component is in active use by a major institution, has a permissive licence, and has been seen in production. The complexity Athlon Eval adds is in the items, the rubrics, and the multilingual coverage – not in inventing new infrastructure.

---

## Chapter 27 – Documentation and governance

### 27.1 Datasheets for datasets (Gebru et al. 2018)

The standard for documenting any ML dataset, modelled on electronics datasheets **[verified, source 23]**. Structured around motivation, composition, collection process, labelling, distribution, and maintenance. Athlon Eval ships a datasheet for each track in `docs/eval-cards/` alongside the items.

### 27.2 Model cards (Mitchell et al. 2018)

The complement to datasheets, but for models **[verified, source 24]**. Athlon Eval doesn't ship a model card (it isn't a model), but it consumes them: every model evaluated on the leaderboard is listed with a link to its model card so reviewers can check training data and intended use.

### 27.3 System cards

The pipeline-level documentation tying datasets, models, and decisions into one DAG **[inferred; "system card" is not a single canonical paper but a practice popularised by OpenAI's GPT-4 and successor releases]**. Used by OpenAI for its frontier-model releases since 2023. For Athlon Eval, the system card describes the entire scoring pipeline (item → harness → judge → aggregator → leaderboard) and is the artefact regulators would inspect under EU AI Act audits.

### 27.4 Eval cards (proposed)

There is no agreed standard yet for an "eval card" – documentation that describes the *benchmark* rather than the *dataset* or the *model*. Athlon Eval should publish one anyway, as a contribution to the community. Suggested fields:

- Construct definition (per track)
- Authoring protocol (per track)
- Inter-annotator agreement statistics
- Baseline matrix
- Confidence interval method
- Reproducibility checklist
- Retirement criteria
- Known limitations
- Versioning policy
- Licensing

### 27.5 Licensing and access

| Asset | Recommended licence |
|---|---|
| Items in the public fold | CC-BY-4.0 |
| Code in the harness | MIT or Apache 2.0 |
| Reference answers | CC-BY-4.0 (public fold), proprietary (held-out fold) |
| Leaderboard data | CC-BY-4.0 |
| Aggregated rater feedback | CC-BY-4.0 |

The held-out fold is explicitly *not* published. Vendors submit model outputs via a hosted API that runs scoring server-side; only aggregate scores leave the benchmark host. This is the same pattern SWE-Bench Verified and HumanEval+ use to mitigate contamination **[inferred from sources 12, 16]**.

### 27.6 Regulatory context

EU AI Act and NIST AI Risk Management Framework both reference dataset and model documentation as table stakes **[inferred from regulatory texts; not tied to a single arXiv source]**. For Athlon Eval, the practical implications:

- **EU AI Act** – fitness coaching is not currently a high-risk category, but medical-grade use cases (Track F red flags) brush against Annex III. The eval card must make the boundary explicit.
- **NIST AI RMF** – the framework's "Map → Measure → Manage → Govern" loop maps cleanly onto Athlon Eval's eight-stage lifecycle. Aligning publicly with NIST RMF would help US chain procurement teams adopt the benchmark.
- **GDPR** – every item is synthetic or anonymised-with-consent. This is enforced at authoring time, not after the fact.

---

## Chapter 28 – Lessons from adjacent domains

### 28.1 Medical: MedQA, MultiMedQA, MedHELM

MedQA is the most-used medical LLM benchmark – multiple-choice questions from the USMLE (commonly cited as ~11,450 development / 1,273 test items, totalling ~12,723; the exact split should be confirmed against Jin et al. 2020 before external citation) **[inferred, source 14]**, with a 60 % accuracy threshold widely treated as the bar for clinical relevance **[verified, source 14]**. MultiMedQA bundles MedQA with 7 other datasets including PubMedQA, MMLU clinical, and MedMCQA **[verified, source 14]**. MedHELM ports the HELM holistic framework to medical tasks.

What Athlon Eval should borrow:
- **Threshold-based reporting** – publish a "qualified" threshold per track and report which models clear it. MedQA's 60 % framing is the right cognitive shorthand for procurement teams.
- **Bundling pattern** – MultiMedQA shows how to combine multiple sub-benchmarks under one umbrella with one headline score.
- **Expert validation depth** – the CSEDB benchmark used 32 specialist physicians to develop and revise 2,069 open-ended Q&A items across 26 clinical departments **[verified, source 19]**. Athlon Eval Track F should aim for proportional rigor: ~10 fitness/clinical experts across 7 harm categories.

What Athlon Eval should *not* borrow:
- The pure-MCQ format. Coaching is not a multiple-choice task, and Tracks B/C/E/G need to escape MCQ to have validity.

### 28.2 Legal: LegalBench

LegalBench has 162 tasks contributed by 40 lawyers and legal scholars covering six types of legal reasoning **[verified, source 18]**. The construction model is the gold standard for what Athlon Eval should aspire to in Tracks B and C: distributed expert authorship across many small contributions, rather than centralised authoring.

Practical implications:
- **Build a contributor protocol.** LegalBench published exactly what a contributor needs to submit (task statement, examples, expected answer, scoring rule). Athlon Eval needs the same for fitness experts.
- **Bias toward "subject matter experts decide what is interesting".** LegalBench tasks measure "things lawyers find interesting". Athlon Eval should measure "things coaches and operators find interesting" rather than what an academic ML researcher would find tractable.

### 28.3 Multilingual: M3Exam, MEGA, FLORES+

M3Exam is the architectural model: 12,317 questions in 9 languages including extremely low-resource languages like Javanese, sourced from real official exams rather than translations **[verified, source 17]**. The lesson is **never translate items as the primary multilingual strategy**. Translation imports the translator's biases and creates artefacts that confound model differences.

For Athlon Eval, this means:
- Track A items in PL, DE, ES, FR, PT, AR are authored in-language by coaches who speak those languages, not translated from English.
- Translation is allowed only for distractors in MCQs, where the construct is robust.
- Per-language scores are reported separately; there is no "average across languages" headline.

### 28.4 Safety: HarmBench, AgentHarm

HarmBench has 510 behaviours across 4 functional categories and 7 semantic harm domains **[verified, source 20]**, with annotation by expert red-teamers complemented by an LLM-classifier validation step reported to reach >93 % agreement with human evaluators **[inferred, source 20; the headline counts are verified against the HarmBench paper, but the >93 % figure was not independently re-verified against the original Mazeika et al. text]**. AgentHarm extends to agentic settings with 110 unique + 330 augmented behaviours across 11 harm categories using 104 distinct tools, each with a harmful and benign counterpart **[verified, source 21]**.

The two patterns Athlon Eval Track F must adopt:
- **Harmful + benign pairs**, so the benchmark measures both refusal *and* over-refusal. AgentHarm's pairing methodology is the cleanest implementation **[verified, source 21]**.
- **Classifier-augmented validation**, where an LLM classifier scores model outputs for harmful compliance with human spot-check. This is the only way safety scoring scales to ~150 prompts × 7 languages × ~10 models.

### 28.5 Code: HumanEval → LiveCodeBench

The cautionary tale. HumanEval (Chen et al. 2021) defined the bar for code-LLM evaluation for years; by 2024 it was contaminated to the point of being misleading. Independent measurements show **8–18 % HumanEval overlap** in widely-used pre-training corpora like RedPajama-Data-1T and StarCoder-Data **[verified, source 27]**. LiveCodeBench replaced HumanEval with a temporally segmented evaluation: every problem is tagged with its release date, and a model is scored only on problems released after its training cutoff **[verified, source 22]**.

The lesson for Athlon Eval is the held-out + time-tagged architecture from §25.5. Without it, every track will eventually become its own HumanEval.

---

## Chapter 29 – Athlon Eval Stage 1 implementation blueprint

### 29.1 Track A (Knowledge) – construction recipe

| Step | Detail |
|---|---|
| **Construct** | Exercise-science knowledge: ACSM FITT, NSCA periodisation, contraindications, nutrition basics, club-ops vocabulary |
| **Item type** | 4-option MCQ |
| **Item count** | 1,500 unique items per language (EN, PL, DE, ES, FR, PT, AR) |
| **Authoring** | Protocol A (template + 2 coaches per item) |
| **Validation target** | Cohen's kappa ≥0.7 between coaches |
| **Scoring** | Accuracy + ECE; Bayesian credible intervals |
| **Runtime** | lm-evaluation-harness |
| **Public/held-out split** | 80 % public / 20 % held-out |
| **Publication** | Items and scoring on GitHub + HF Datasets, paper to ACL/NeurIPS D&B |
| **Cost estimate (one language)** | ~USD 15k expert authoring + ~USD 2k API spend for baselines |

### 29.2 Track D (Operational support) – construction recipe

| Step | Detail |
|---|---|
| **Construct** | Club FAQ deflection, billing edge cases, cancellation handling, escalation routing |
| **Item type** | Member query + corpus context + expected outcome (answer / clarify / escalate / refuse / decline-out-of-scope) |
| **Item count** | 500 queries per language; 1 shared synthetic club-ops corpus |
| **Authoring** | Protocol B (synthetic corpus + 1 club operator per language reviewing 10 % slice) |
| **Validation target** | Krippendorff's alpha ≥0.6 on reviewed slice |
| **Scoring** | Closed-form deflection accuracy + rubric on escalation reasoning + cost + latency |
| **Runtime** | Inspect AI |
| **Public/held-out split** | 50 % public / 50 % held-out |
| **Publication** | Public fold released; held-out fold gated behind hosted scoring |
| **Cost estimate** | ~USD 10k corpus build + ~USD 5k operator review + ~USD 3k API spend |

### 29.3 Track F (Safety and compliance) – construction recipe

| Step | Detail |
|---|---|
| **Construct** | Refuses or escalates prompts that meet the harm taxonomy, regardless of phrasing/language/role-play; completes benign prompts that share surface features |
| **Item type** | Harmful + benign prompt pairs (AgentHarm pattern) |
| **Item count** | 150 harmful + 150 benign prompts per language |
| **Authoring** | Protocol C (exercise physiologist drafts + clinician review) |
| **Validation target** | Cohen's kappa ≥0.7 per harm class |
| **Scoring** | Refusal accuracy on harmful + benign-precision (1 − over-refusal) + jailbreak robustness (5 attempted variants per harmful prompt) |
| **Runtime** | Inspect AI sandboxed |
| **Public/held-out split** | 60 % public / 40 % held-out |
| **Hard gate** | Models below threshold are excluded from the overall ranking, not just scored lower |
| **Cost estimate** | ~USD 20k physiologist + clinician + ~USD 4k API spend |

### 29.4 Cross-track infrastructure

- **Eval card** format published in repo root
- **Reproducibility checklist** in CI as a blocking test
- **Per-language sub-leaderboards** in addition to the headline score
- **Cost and latency reporting** standardised across tracks
- **Variance reporting** at ≥5 reruns per scenario
- **Judge ensemble** for any LLM-as-judge usage (≥2 model families)

### 29.5 Recommended stack (recap)

| Layer | Tool |
|---|---|
| Track A runtime | lm-evaluation-harness |
| Track D runtime | Inspect AI + RAGAS auxiliary |
| Track F runtime | Inspect AI sandboxed |
| Reporting taxonomy | HELM-style 7 metrics |
| Item hosting | Hugging Face Datasets (Croissant metadata) |
| Code hosting | GitHub (`qodeca/athloneval`) |
| Leaderboard | Static site at athloneval.ai |
| CI | GitHub Actions, weekly model-snapshot run |

### 29.6 90-day Stage 1 build plan

| Weeks | Workstream |
|---|---|
| 1–2 | Eval card v1, harm taxonomy, construct definitions per track |
| 3–6 | Track A item authoring (EN first), Track D corpus build |
| 7–8 | Cross-language Track A authoring (PL, DE, ES, FR, PT, AR) |
| 9 | Inter-annotator agreement validation pass |
| 10 | Pilot run on 5+5 baseline models |
| 11 | Pilot fixes, item rewrites, format calibration |
| 12 | Frozen v1.0.0 release: GitHub, HF Datasets, paper draft, leaderboard live |

### 29.7 Risks and mitigations

| Risk | Mitigation |
|---|---|
| Expert authoring slips schedule | Use Qodeca's existing client-coach network; pre-pay for 50 % of capacity |
| Multilingual quality uneven | Per-language IAA gate; do not ship a language below the kappa floor |
| Judge bias contaminates Tracks D/F | Mandatory ≥2-family judge ensemble + 10 % human spot-check |
| Stage-1 scope creeps into Stage 2 tracks | Hard freeze on Tracks B, C, E, G until v1 ships |
| First leaderboard makes the wrong vendor look good | Publish raw per-item CSVs so anyone can re-aggregate; transparent is robust |
| Contamination of public fold within months | Held-out fold + quarterly rotation + time-tagged items |
| Reviewer rejection at NeurIPS | Submit eval card and reproducibility checklist with the paper, not as appendix |

---

## Chapter 30 – References

References are numbered in the order they first appear in the text. Each entry tags whether it was read in full **[full]**, abstract-only via web fetch **[abstract]**, or via documentation/blog **[docs]**.

*Note on revisions*: this reference list was restructured after a fact-check pass on 2026-04-11 to (a) split previously bundled entries into separate numbered references, (b) correct an arXiv-ID misattribution in the medical section, and (c) add primary sources for several claims that had been tagged to secondary sources.

1. *The Anatomy of an LLM Benchmark*, Cameron Wolfe, 2025. Substack article on benchmark design principles. **[docs]**. https://cameronrwolfe.substack.com/p/llm-bench
2. Liang, P., Bommasani, R., Lee, T., et al. *Holistic Evaluation of Language Models* (HELM). arXiv:2211.09110, 2022. The 7-metric × 16-scenario foundational framework. **[abstract]**. https://arxiv.org/abs/2211.09110
3. *Goodhart's Law in AI Leaderboards*. Collinear AI blog, 2025. Documents Arena leaderboard gaming and benchmark saturation. **[docs]**. https://blog.collinear.ai/p/gaming-the-system-goodharts-law-exemplified-in-ai-leaderboard-controversy
4. *NeurIPS 2025 Datasets & Benchmarks Track Call for Papers*. Submission requirements including Croissant metadata. **[docs]**. https://neurips.cc/Conferences/2025/CallForDatasetsBenchmarks
5. *RIFT: A Rubric Failure-Mode Taxonomy*. OpenReview, 2025. Inter-annotator agreement statistics for rubric annotation. **[abstract]**. https://openreview.net/pdf/9107ac388dcdc94145fb46a6c32b9e871a46aa1d.pdf
6. *MMLU – Wikipedia summary including the 2024 ground-truth audit*. **[docs]**. https://en.wikipedia.org/wiki/MMLU
7. *Self-Preference Bias in LLM-as-a-Judge*. arXiv:2410.21819, 2024. **[abstract]**. https://arxiv.org/abs/2410.21819
8. EleutherAI. *lm-evaluation-harness*. GitHub repository and task documentation. **[docs]**. https://github.com/EleutherAI/lm-evaluation-harness
9. Zheng, L., Chiang, W.-L., et al. *Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena*. arXiv:2306.05685, 2023. The original LLM-as-judge paper. **[abstract]**. https://arxiv.org/abs/2306.05685
10. DeepEval / Confident AI. *DeepEval: The LLM Evaluation Framework*. GitHub and docs. **[docs]**. https://github.com/confident-ai/deepeval
11. *Promptfoo*. GitHub repository and docs. Acquired by OpenAI in March 2026. **[docs]**. https://github.com/promptfoo/promptfoo and https://openai.com/index/openai-to-acquire-promptfoo/
12. *A Comprehensive Survey of Contamination Detection Methods in Large Language Models*. arXiv:2404.00699, 2024. **[abstract]**. https://arxiv.org/abs/2404.00699
13. Chiang, W.-L., et al. *Chatbot Arena: An Open Platform for Evaluating LLMs by Human Preference*. arXiv:2403.04132, 2024. Bradley-Terry methodology and bootstrap CIs. **[abstract]**. https://arxiv.org/abs/2403.04132
14. *MultiMedQA, MedQA, MedHELM* – Hugging Face Open Medical-LLM Leaderboard and supporting papers. **[docs]**. https://huggingface.co/blog/leaderboard-medicalllm
15. Bowyer, S., Aitchison, L., Ivanova, D. *Position: Don't Use the CLT in LLM Evals With Fewer Than a Few Hundred Datapoints*. arXiv:2503.01747, ICML 2025 spotlight. **[abstract]**. https://arxiv.org/abs/2503.01747
16. Miller, E. *Adding Error Bars to Evals: A Statistical Approach to Language Model Evaluations*. arXiv:2411.00640, 2024. **[abstract]**. https://arxiv.org/abs/2411.00640
17. Zhang, W., et al. *M3Exam: A Multilingual, Multimodal, Multilevel Benchmark*. arXiv:2306.05179, 2023. **[abstract]**. https://arxiv.org/abs/2306.05179
18. Guha, N., et al. *LegalBench: A Collaboratively Built Benchmark for Measuring Legal Reasoning*. arXiv:2308.11462, NeurIPS 2023. **[abstract]**. https://arxiv.org/abs/2308.11462
19. *Clinical Safety-Effectiveness Dual-Track Benchmark (CSEDB): A Novel Evaluation Benchmark for Medical LLMs Illuminating Safety and Effectiveness in Clinical Domains*. arXiv:2507.23486, 2025 (npj Digital Medicine). The 32-physician / 2,069-item / 26-clinical-department construction. **[abstract]**. https://arxiv.org/abs/2507.23486
20. Mazeika, M., et al. *HarmBench: A Standardized Evaluation Framework for Automated Red Teaming and Robust Refusal*. arXiv:2402.04249, 2024. 510 behaviours, 4 functional categories, 7 semantic harm domains. **[abstract]**. https://arxiv.org/abs/2402.04249
21. Andriushchenko, M., et al. *AgentHarm: A Benchmark for Measuring Harmfulness of LLM Agents*. arXiv:2410.09024, ICLR 2025. 110 unique + 330 augmented behaviours, 11 harm categories, 104 distinct tools, harmful + benign pairs. **[abstract]**. https://arxiv.org/abs/2410.09024
22. Jain, N., et al. *LiveCodeBench: Holistic and Contamination-Free Evaluation of Large Language Models for Code*. arXiv:2403.07974, 2024. Time-segmented contamination-resistant evaluation. **[abstract]**. https://arxiv.org/abs/2403.07974
23. Gebru, T., et al. *Datasheets for Datasets*. arXiv:1803.09010, 2018 (Communications of the ACM 2021). The standard for ML dataset documentation. **[abstract]**. https://arxiv.org/abs/1803.09010
24. Mitchell, M., et al. *Model Cards for Model Reporting*. arXiv:1810.03993, ACM FAT* 2019. The standard for ML model documentation. **[abstract]**. https://arxiv.org/abs/1810.03993
25. UK AI Security Institute. *Inspect AI – A framework for large language model evaluations*. Project site and GitHub. **[docs]**. https://inspect.aisi.org.uk/ and https://github.com/UKGovernmentBEIS/inspect_ai
26. Landis, J. R., Koch, G. G. *The Measurement of Observer Agreement for Categorical Data*. Biometrics 33(1):159-174, 1977. The canonical kappa-strength convention (≥0.61 substantial, ≥0.81 almost perfect). **[docs]**. https://pubmed.ncbi.nlm.nih.gov/843571/
27. Yang, S., et al. *Rethinking Benchmark and Contamination for Language Models with Rephrased Samples*. arXiv:2311.04850, 2023. Primary source for the HumanEval 8–18 % overlap measurement against RedPajama-Data-1T and StarCoder-Data. **[abstract]**. https://arxiv.org/abs/2311.04850
28. Es, S., James, J., Espinosa-Anke, L., Schockaert, S. *RAGAS: Automated Evaluation of Retrieval Augmented Generation*. arXiv:2309.15217, 2023. Reference-free RAG evaluation framework. **[abstract]**. https://arxiv.org/abs/2309.15217

---

*End of Part III. The proposed next read for an Athlon Eval Stage-1 builder is to revisit [Part II Chapter 16](part-2-industry-frame.md) for the seven-track positioning, then return here to [Chapter 29](#chapter-29--athlon-eval-stage-1-implementation-blueprint) for the build plan.*

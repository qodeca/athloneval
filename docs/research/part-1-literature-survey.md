# Part I – Academic literature survey

*Part 1 of 2 of the fitness LLM benchmarks research report. Landing page and executive summary live in [`../fitness-llm-benchmarks-research.md`](../fitness-llm-benchmarks-research.md). The industry-frame counterpart is [`part-2-industry-frame.md`](part-2-industry-frame.md).*

*This part surveys the peer-reviewed and preprint literature on LLM benchmarks relevant to fitness, sports, exercise, wearables, nutrition, physiotherapy, and biomechanics, and synthesises the gaps a new benchmark could fill. Compiled 2026-04-11 from ~30 web searches and ~15 paper fetches.*

---

## Part 1 – Direct sports/fitness benchmarks

### 1.1 SportQA (2024) – the first sports-language benchmark

| Field | Value |
|---|---|
| **Venue** | NAACL 2024 (Xia et al., arXiv:2402.15862) |
| **Modality** | Text only – multiple-choice QA |
| **Size** | ~70,000 MCQs across 3 difficulty levels |
| **Code** | https://github.com/haotianxia/SportQA |

**Structure**:
- **Level 1** – 21,385 questions on factual/historical knowledge
- **Level 2** – 45,685 questions on rules and tactics across **35 sports**
- **Level 3** – 3,522 scenario questions on **6 sports**: soccer, basketball, volleyball, tennis, table tennis, American football. Level 3 splits into Easy/Hard × Single-hop/Multi-hop

**Headline results (5-shot CoT)**:

| Model | L1 | L2 | L3 Hard Single | L3 Hard Multi |
|---|---|---|---|---|
| GPT-3.5 | 71.97% | 66.52% | 23.60% | 14.64% |
| GPT-4 | 85.63% | 78.82% | 64.08% | 23.01% |
| **Human baseline** | **96.63%** | **96.02%** | **~92%** | **~92%** |

**Construction**: Level-1 and Level-2 used templated generation with semantic distractor libraries, modified by 36 student-athletes. Level-3 was written entirely by domain-expert coaches.

**What it tests**: Rule comprehension and tactical reasoning. What it does *not* test: program design, exercise prescription, safety, adaptation, biomechanics, or anything fitness/health-coaching related.

---

### 1.2 SPORTU (ICLR 2025) – multimodal sports understanding

| Field | Value |
|---|---|
| **Venue** | ICLR 2025 (Xia et al., arXiv:2410.08474) |
| **Modality** | Text + video (multimodal) |
| **Size** | 900 MCQs (text) + 1,701 slow-motion clips with 12,048 QA pairs (video) |
| **Sports** | 7 (video component) |
| **Code** | https://github.com/chili-lab/SPORTU |

SPORTU is the explicit multimodal sequel to SportQA. Tasks range from simple sport recognition to foul detection and rule application from video clips.

**Headline results**: GPT-4o scored **71% on SPORTU-text** (still well below human); Claude 3.5 Sonnet scored **52.6% on the hardest video tasks**. Across 13 MLLMs tested (7 proprietary, 6 open-source), every model fell substantially short of human performance, particularly on fine-grained temporal reasoning and rule application.

**What it tests**: Visual sports comprehension. Still not coaching or fitness prescription.

---

### 1.3 SportR (late 2025) – chain-of-thought sports reasoning

| Field | Value |
|---|---|
| **Venue** | arXiv:2511.06499 (Nov 2025) |
| **Modality** | Image + video MLLM reasoning |
| **Size** | 4,789 images + 2,052 videos + **6,841 human-authored chain-of-thought annotations** |

The newest entry in the sports-MLLM line. Unique contribution: multi-step CoT annotations and manual bounding boxes for visual grounding. Tasks range from infraction identification to penalty prediction. Paper reports poor baseline performance and only modest improvement from SFT + RL fine-tuning, indicating a meaningful capability gap.

---

### 1.4 SportsGPT (Dec 2025) – RAG-grounded motion assessment framework

| Field | Value |
|---|---|
| **Venue** | arXiv:2512.14121 |
| **Modality** | Skeleton/motion capture + RAG-grounded text generation |
| **Components** | MotionDTW (keyframe alignment) + KISMAM (deviation metrics) + SportsRAG |
| **Knowledge base** | ~6B tokens: 200+ sports-science textbooks, 50,000 expert QA pairs, 1,000 historical analysis reports |

SportsGPT is more an architecture than a benchmark, but it bundles its own evaluation on **standing long jump** and **100m sprint phases** (Start, Acceleration, Mid, Late). Scored on a 1–5 Likert scale across Accuracy, Comprehensiveness, Professionalism, and Feasibility. Reported scores: 3.80/5 Accuracy vs. 3.00/5 for Claude 4.5 Opus, demonstrating that a domain-specialised RAG stack beats frontier general models on technical motion analysis.

**Why this matters for fitbench**: it's currently the most ambitious sports-coaching-specific evaluation rubric in the literature, even if the task space is narrow.

---

### 1.5 LLM-SPTRec (Nature Scientific Reports, 2026) – personalised training plans with knowledge graphs

A recent knowledge-graph-augmented LLM framework for personalised sports training plans. Combines multi-source heterogeneous user data with domain knowledge graphs. Surpasses collaborative filtering, sequential models, and general-purpose LLMs on plan coherence, goal relevance, and predicted user satisfaction. **Not a public benchmark, but a closely-related evaluation methodology that fitbench could adopt or extend.**

---

## Part 2 – Wearable-data health benchmarks (fitness-adjacent)

These benchmarks all include fitness/activity as one evaluation axis among several. They're the closest thing to "fitness LLM benchmarks" that exist in rigorous peer-reviewed form.

### 2.1 Health-LLM (Jan 2024) – the foundational wearable benchmark

| Field | Value |
|---|---|
| **Authors** | MIT + Google Research (Kim et al., arXiv:2401.06866) |
| **Tasks** | **10 consumer health prediction tasks** across 4 categories: mental health, **activity**, metabolic, sleep |
| **Datasets** | PMData, LifeSnaps, GLOBEM, AW_FB (4 public wearable datasets) |
| **Models** | 12 state-of-the-art LLMs including GPT-3.5, GPT-4, Gemini-Pro |
| **Key finding** | Fine-tuned **HealthAlpaca** (based on Alpaca) matches GPT-3.5/GPT-4/Gemini-Pro on 8/10 tasks |

The Health-LLM framework is the closest thing to a "prediction accuracy" benchmark for LLMs reasoning over fitness sensor data. Activity tasks include step prediction, activity classification, and similar.

---

### 2.2 PH-LLM (Nature Medicine, Aug 2025) – Google's fitness/sleep LLM

| Field | Value |
|---|---|
| **Publication** | Nature Medicine 2025 (Merhej et al.), preprint arXiv:2406.06474 |
| **Base model** | Gemini Ultra, fine-tuned for sleep and fitness |
| **Evaluation** | 3 benchmark datasets + **857 real-world case studies** from anonymised Fitbit production data |

PH-LLM is the most ambitious fitness-specific LLM evaluation to date. Three benchmark datasets cover:
1. Expert domain knowledge (multiple-choice exams)
2. Personalised insight/recommendation generation from longitudinal sensor data
3. Self-reported sleep-quality prediction

**Headline results**:
- **Fitness MCQ exam: PH-LLM 88% vs. human experts 71%** (PH-LLM beat experts)
- **Sleep MCQ exam: PH-LLM 79% vs. human experts 76%** (parity)
- On the 857 open-ended case studies, PH-LLM achieved parity with human experts on fitness-related tasks and improved over base Gemini on sleep insights

**Why this matters for fitbench**:
- This is the first peer-reviewed evidence that a fine-tuned LLM can match human experts on a fitness knowledge exam
- But it uses Google-internal Fitbit data that isn't publicly reproducible – **there is no open version of this benchmark**
- A Nature Medicine follow-up in 2026 ("Improving AI coaching with Gemini using real-world Fitbit data") extends the work

---

### 2.3 HealthSLM-Bench (Sept 2025) – small language models on wearables

| Field | Value |
|---|---|
| **Reference** | arXiv:2509.07260 |
| **Models** | 9 small language models |
| **Tasks** | 8 health prediction tasks |
| **Datasets** | 3 real-world mobile/wearable datasets |
| **Approaches** | Zero-shot, few-shot, instruction fine-tuning |

Key finding: SLMs can match LLM performance on many health prediction tasks while offering large efficiency and privacy gains. Fitness/activity tasks are included but not the primary focus.

---

### 2.4 PHIA (Nature Communications 2025) – Personal Health Insights Agent

PHIA combines multi-step reasoning with **code generation** and information retrieval to analyse behavioural health data. It ships with two benchmark datasets totalling **~4,000 health-insight questions**. Unique contribution: forces the LLM to produce executable code for data analysis rather than free-text answers, making evaluation more objective.

---

## Part 3 – Adjacent benchmarks (nutrition, physiotherapy, biomechanics)

### 3.1 Nutrition

| Benchmark | Size | Key finding |
|---|---|---|
| **NutriBench** (arXiv:2407.12843) | 11,857 human-verified meal descriptions from 11 countries, labelled with carbs/protein/fat/calories | GPT-4o with CoT **beat professional nutritionists on carbohydrate estimation accuracy** |
| **Registered Dietitian exam** (Sci Reports 2024) | RD exam questions | GPT-4o with CoT-SC prompting led; domain-specialised models (FoodyLLM, FoodSky) beat general models by 20–50 percentage points |
| **FoodSky** | Chinese National Chef + Dietetic exams | 83.3% and 91.2% accuracy, beating general LLMs |
| **"When LLMs Can't Help"** (arXiv:2511.20652) | Real-world nutrition deployment study | Intrinsic eval wins *did not* transfer to real-world dietary adherence or engagement |

**Takeaway for fitbench**: The nutrition-benchmark space is 1–2 years ahead of fitness. It has: large public datasets, domain-specialised fine-tuned models that beat frontier LLMs, and published work showing intrinsic benchmarks don't predict real-world outcomes. fitbench should borrow all three patterns.

---

### 3.2 Physiotherapy and rehabilitation

| Work | Contribution |
|---|---|
| **Physio** (arXiv:2401.01825) | LLM-based physiotherapy advisor with source citation |
| **Clinical rehab study** (MDPI Appl Sci 2025) | Compared GPT-4.1 and DeepSeek-V2 against 90 physiotherapy students on 60 clinical questions across LBP, MS, frozen shoulder, knee OA |
| **"LLMs in physical therapy" review** (Frontiers Public Health 2024) | Position paper arguing PT must adapt to LLMs |
| **"Intelligent clinical support for personalised sport training rehabilitation"** (Springer 2025) | Applies LLMs to sport rehab with clinical decision support framing |

No public benchmark exists here – all work is small-scale comparison studies.

---

### 3.3 Biomechanics and motion analysis

| Work | Contribution |
|---|---|
| **BiomechGPT** (arXiv:2505.18465) | Multimodal foundation model trained on 30+ hours of movement capture from ~500 participants including those with movement impairments. Handles activity recognition, impairment identification, diagnosis, clinical outcome scoring, walking measurement. |
| **MotionBench** (CVPR 2025, arXiv:2501.02955) | General video motion understanding, not sports-specific |
| **MoVid-Bench** | Body-part motion awareness, sequential analysis, direction awareness |

BiomechGPT is the most relevant here because it bridges motion capture data with natural language reasoning – a direction fitbench may want to cover.

---

## Part 4 – Coaching-quality evaluation studies (individual papers)

These are not benchmarks per se – they're one-off studies where researchers hand-constructed a rubric, generated outputs with LLMs, and had experts rate them. Collectively they *describe the shape of what a real benchmark would look like*.

### 4.1 Strength/hypertrophy program generation

- **"A professional assessment of training plans for muscle hypertrophy and maximal strength developed by generative AI"** (PMC12492345, 2025): 10 experienced coaches rated plans from GPT-3.5, GPT-4 via Copilot, and Google Gemini on a 1–5 Likert scale across **27 criteria**. GPT-4 via Copilot scored highest on exercise selection, order, intensity, rep range, volume, rest periods, and set endpoint. All models fell short of expert-designed plans.
- **"Reproducibility and quality of hypertrophy-related training plans generated by GPT-4 and Google Gemini"** (PMC11963122, 2025): Tested whether repeated identical prompts produced reproducible plans. Found significant variance.
- **"AI Hypertrophy Coaching with Wearable Sensors"** (arXiv:2512.11854, Dec 2025): Latest entry combining LLM coaching with sensor data.
- **"Comparative evaluation of ChatGPT versions in training program design"** (PMC12797729, 2025): Cross-version evaluation across GPT-3.5/4/4o on scientific approach, accuracy, and practical applicability.

### 4.2 Running/endurance

- **"Exploring LLM as Interactive Sports Coach: Single-Subject Half Marathon Preparation"** (arXiv:2509.26593, 2025): Two-month case study (July-Sept 2025) of one runner using an LLM coach. Subject improved from 2km at 7:54/km pace to 21.1km at 6:30/km. Identified critical gaps: **no real-time sensor integration, text-only feedback, user-initiated motivation, limited safety guardrails**. The authors explicitly call for "persistent athlete models with explicit safety constraints" – which is what a proper benchmark would evaluate.
- Multiple popular-press evaluations (Time, Tom's Guide, Outside) found GPT-3.5/4 frequently violating progression principles: recommending 19-mile runs the day before a marathon, suggesting 34-mile training runs for intermediates, miscalculating workout durations, omitting deload weeks.

### 4.3 Direct human-vs-LLM comparisons

- **"ChatGPT Outperforms Personal Trainers in Answering Common Exercise Training Questions"** (PMC12912680, 2025): 9 PTs submitted FAQs → 18 PTs + 9 experts rated ChatGPT vs. human responses on scientific correctness, actionability, comprehensibility. **ChatGPT won on all six criteria** for question-answering.
- **"GPT-4 as a virtual fitness coach"** (PMC12261634, 2025): GPT-4 beat human coaches on personalisation; coaches slightly beat GPT-4 on effectiveness/safety/comprehensiveness (differences not statistically significant).

### 4.4 Sport safeguarding

- **"How accurately do LLMs interpret sport safeguarding principles: IOC framework evaluation"** (BMC Sports Sci Med Rehab 2026): Tests LLMs against the International Olympic Committee safeguarding framework. First dedicated safety-oriented LLM evaluation in sport.

---

## Part 5 – Evaluation methodology literature

Two 2025 scoping reviews in JMIR are the most important meta-level work. Both explicitly identify the absence of a fitness/exercise LLM benchmark as a major research gap.

### 5.1 "Evaluation Strategies for LLM-Based Models in Exercise and Health Coaching: Scoping Review"

**Citation**: JMIR 2025;27:e79217 (PMC12520646) – **the single most important paper for fitbench to read in full**.

**Findings**:
- Reviewed **20 peer-reviewed studies** from March 2023 to July 2025
- **75% used proprietary ChatGPT** (15/20); 20% Gemini; open-source models minimal
- **80% used human ratings** (16/20); 40% used automated metrics (8/20)
- **Only 45% reported interrater reliability**; only 40% used real-world data
- Authors developed a custom **Evaluation Rigor Score (ERS)** – 5-point instrument covering: (1) validation context (real-world vs. hypothetical), (2) data authenticity, (3) instrument validity, (4) interrater reliability, (5) comparative benchmarks
- **Median ERS: 2.5/5. 55% of studies classified as low-rigor**

**Recommendations**:
1. Integrate RAG with evidence-based sources (ACSM, WHO guidelines)
2. Use an Adaptive Precise Boolean Framework (binary Yes/No) for scalable assessment
3. Multidimensional validation combining technical + human-centred methods
4. Privacy-first architecture (federated learning, local deployment)

### 5.2 "Using LLMs to Enhance Exercise Recommendations and Physical Activity: Scoping Review"

**Citation**: JMIR Medical Informatics 2025;13:e59309 (PMC12133071).

**Findings**:
- 598 papers identified, **11 included** after screening (1.8% inclusion rate – indicating sparse literature)
- 55% used ChatGPT-based models
- **LLMs "fall short of professional-level expertise, particularly in aligning with ACSM guidelines"**
- 73% used expert evaluation + user feedback
- ChatGPT: 41.2% comprehensiveness, 90.7% accuracy in one study
- GPT-4 "just-in-time adaptive interventions" rated highest in quality/effectiveness/emotional impact, surpassing healthcare professionals

**Concludes**: "Developing benchmarking frameworks to systematically evaluate LLM-generated exercise recommendations will be essential for ensuring accuracy, safety, and adaptability."

This is essentially a formal call-to-arms for what `fitbench` could be.

### 5.3 Foundational paper

**"Large Language Models in Sport Science & Medicine: Opportunities, Risks and Considerations"** (arXiv:2305.03851, May 2023). The earliest serious discussion of LLMs in this domain. Useful for framing/motivation but predates all actual benchmark work.

---

## Part 6 – Commercial landscape

Relevant because commercial products are currently the *de facto* benchmark for what "fitness LLM coaching" looks like in the wild.

| Product | LLM stack | Notes |
|---|---|---|
| **WHOOP Coach** | OpenAI GPT-4, fine-tuned on anonymised member data | Conversational layer over WHOOP biometrics + sports-science database. Announced via OpenAI case study. Some users report the outputs could be reproduced by a simple Python script – meaning the LLM value-add isn't always obvious. |
| **Fitbit AI Coach / Google Labs "Health Coaching"** | PH-LLM / Gemini | Most sophisticated commercial deployment; backed by peer-reviewed Nature Medicine research |
| **Strava Intelligence / Strava AI Coach** | Undisclosed ML/LLM mix | Generative commentary on performances. Users report v1 is shallow – largely parrots pace comparisons |
| **Garmin Connect AI features** | Undisclosed | Discrete pre-defined insights behind a paywall, not conversational |
| **Runna** | Undisclosed | Commercial personalised running plan generator (not LLM-native) |
| **2PEAK** | Undisclosed ML | AI-based training for triathlon and cycling (predates LLM era) |

The direction of travel: Fitbit and WHOOP are integrating LLMs as a *foundational conversational layer* rather than an add-on menu item. Strava and Garmin lag.

---

## Part 7 – Datasets on Hugging Face (community)

None of the fitness datasets on Hugging Face qualify as evaluation benchmarks. They're training-data sources – community-uploaded, mixed quality, often synthetic. Relevant entries:

| Dataset | Notes |
|---|---|
| `onurSakar/GYM-Exercise` | Gym exercise dataset |
| `hammamwahab/fitness-qa` | Synthetic Q&A generated with Flan-T5 trained on SQuAD |
| `its-myrto/fitness-question-answers` | 965 web-scraped Q&A pairs |
| `Varick/workout-routine` | Workout routines |
| `syncora/fitness-tracker-dataset` | **Fully synthetic** Apple Watch-style wearable records |
| `chibbss/fitness-chat-prompt-completion-dataset` | Conversational fitness dataset |
| `dexaai/huberman_on_exercise` | Huberman Lab transcript-derived content |

**Takeaway**: There is no curated, expert-validated, public fitness benchmark dataset on Hugging Face. This is a vacant niche.

---

## Part 8 – Gap analysis

Collating what the literature collectively demonstrates is *missing*:

### 8.1 What exists (coverage map)

| Capability | Benchmark | Quality |
|---|---|---|
| Rule / tactical knowledge of sports | SportQA, SPORTU, SportR | Strong (peer-reviewed, public) |
| Multimodal sports video understanding | SPORTU, SportR | Strong |
| Wearable sensor reasoning | Health-LLM, PH-LLM, HealthSLM-Bench, PHIA | Strong but mostly closed or partial |
| Nutrition macronutrient estimation | NutriBench, RD exam, FoodSky | Strong |
| Sport safeguarding (IOC framework) | BMC Sports Sci Med Rehab 2026 | Early-stage |
| Physiotherapy clinical reasoning | Scattered small studies | Weak, no benchmark |
| Biomechanical motion assessment | BiomechGPT, SportsGPT | Early-stage, narrow task space |

### 8.2 What's missing – the fitbench opportunity

No public benchmark currently evaluates any of the following. Each is a plausible track for fitbench:

1. **End-to-end periodised program generation**
   - Input: user goal (e.g., 5k PB, 100kg bench, physique), demographics, equipment, constraints, history
   - Output: full 8–16 week periodised plan
   - Scoring: expert rubric on progressive overload, recovery, exercise selection, volume distribution, reproducibility across runs

2. **Evidence-grounding against ACSM/NSCA/WHO guidelines**
   - Boolean checks: "Does this plan respect ACSM FITT principles for the stated population?"
   - Automated verification via guideline knowledge graph

3. **Adaptation and replanning under disruption**
   - Inject mid-plan perturbations: injury, missed sessions, plateau, equipment change, illness
   - Score the LLM's adjustment quality

4. **Safety and contraindication awareness**
   - Red-flag scenarios: pregnancy, cardiac history, recent surgery, RED-S, overtraining
   - The IOC safeguarding work is a template – extend to exercise prescription

5. **Multi-turn coaching conversation quality**
   - Does the model ask the right questions, update its beliefs, avoid sycophancy, push back when appropriate?
   - Drawing from dialogue-quality benchmarks but grounded in exercise science

6. **Biomechanical form assessment from video**
   - SPORTU covers sport *rule* video; nothing covers *form-quality* video for common movements (squat, deadlift, running gait)
   - Large potential overlap with BiomechGPT direction

7. **Reproducibility and calibration**
   - Same prompt → same (or suitably similar) output
   - Hypertrophy reproducibility paper (PMC11963122) shows this is currently poor
   - Benchmark should explicitly score run-to-run consistency

8. **Real-world outcome correlation**
   - NutriBench's "When LLMs Can't Help" paper is a warning: intrinsic benchmarks don't predict real-world adherence
   - fitbench should eventually include or partner with an RCT-style outcome track

---

## Part 9 – Design recommendations for fitbench

Based on everything above, here's a first cut at what fitbench could be. **These are opinions framed as options, not a decided plan – redirect freely.** These recommendations are superseded and extended by the industry-frame proposal in [`part-2-industry-frame.md`](part-2-industry-frame.md) Chapter 15.

### 9.1 Suggested scope (MVP)

A **structured evaluation benchmark for LLM fitness-coaching quality**, with three complementary tracks:

- **Track A – Knowledge** (easiest, highest leverage): Expert-curated MCQs on exercise science, sports physiology, biomechanics, contraindications, and nutrition basics. ~1,000–3,000 questions across subdomains. Borrows SportQA construction methodology. Closes the gap between SportQA (rules of sport) and medical QA benchmarks (MedQA, MedMCQA) which currently don't cover exercise science.

- **Track B – Program generation** (highest differentiation): Input scenarios (persona, goal, equipment, history, constraints) → LLM generates a plan → scored by an **automated rubric** backed by ACSM/NSCA guideline extraction, plus a **human-expert rubric** for the 1–5 Likert dimensions validated by the hypertrophy studies (exercise selection, order, intensity, volume, rest, progression, etc.). Include reproducibility scoring (same prompt run 5×).

- **Track C – Safety and adaptation** (highest safety value): Red-flag scenarios + mid-plan perturbations. Boolean pass/fail for contraindication detection, Likert for adaptation quality. Partners with the IOC safeguarding framing.

### 9.2 Methodological principles

Drawn directly from the JMIR scoping review's recommendations:
1. **Evidence-grounded** – RAG against ACSM/NSCA/WHO sources with citation tracking
2. **Reproducible** – publish all prompts, responses, expert ratings
3. **Multi-dimensional** – objective metrics + expert Likert + user-outcome (where feasible)
4. **Rigor-aware** – score your own ERS and aim for ≥4/5
5. **Privacy-first** – all data synthetic or fully anonymised
6. **Versioned** – treat the benchmark as software; version questions, rubrics, and leaderboards

### 9.3 Adjacent benchmarks to reuse or extend

- **SportQA's construction pipeline** for Track A (templated + expert-refined)
- **Health-LLM's prompting methodology** for any sensor-reasoning extension
- **PH-LLM's 857-case-study format** for Track B's open-ended evaluation
- **NutriBench's "beat the professional" framing** as a headline metric
- **SportsGPT's MotionDTW + RAG** as an optional video/motion extension

### 9.4 Things to explicitly avoid

- **Don't benchmark only on GPT-4 and Gemini**. Every scoping review flags over-reliance on proprietary frontier models. Include at least 2 open-weight models (Llama, Qwen, Mistral, DeepSeek) from day one.
- **Don't ignore domain-specialised models**. FoodSky / FoodyLLM data shows a 30–50 point gap over general models on nutrition. A fitness-specialised model (FitSky?) will almost certainly beat frontier models once it exists – the benchmark should anticipate that.
- **Don't conflate "knowledge" with "coaching"**. PH-LLM beat human experts on fitness MCQs (88% vs 71%) but still required 857 hand-rated case studies to assess open-ended coaching. One axis does not predict the other.
- **Don't forget injury recurrence / safety**. The half-marathon case study and the ChatGPT-marathon examples show LLMs will cheerfully prescribe dangerous progressions. Safety scoring is non-negotiable.

---

## Part 10 – Complete reference list

### Primary benchmarks

1. **SportQA** – Xia et al., "SportQA: A Benchmark for Sports Understanding in Large Language Models" (NAACL 2024). https://arxiv.org/abs/2402.15862 · https://github.com/haotianxia/SportQA
2. **SPORTU** – Xia et al., "SPORTU: A Comprehensive Sports Understanding Benchmark for Multimodal Large Language Models" (ICLR 2025). https://arxiv.org/abs/2410.08474 · https://github.com/chili-lab/SPORTU
3. **SportR** – "SportR: A Benchmark for Multimodal Large Language Model Reasoning in Sports" (Nov 2025). https://arxiv.org/html/2511.06499
4. **Health-LLM** – Kim et al., "Health-LLM: Large Language Models for Health Prediction via Wearable Sensor Data" (Jan 2024). https://arxiv.org/abs/2401.06866
5. **PH-LLM** – "A personal health large language model for sleep and fitness coaching" (Nature Medicine 2025). https://www.nature.com/articles/s41591-025-03888-0 · preprint https://arxiv.org/abs/2406.06474
6. **HealthSLM-Bench** – "Benchmarking Small Language Models for Mobile and Wearable Healthcare Monitoring" (Sept 2025). https://arxiv.org/abs/2509.07260
7. **PHIA** – "Transforming wearable data into personal health insights using large language model agents" (Nature Communications 2025). https://www.nature.com/articles/s41467-025-67922-y
8. **NutriBench** – "NutriBench: A Dataset for Evaluating Large Language Models on Nutrition Estimation from Meal Descriptions" (2024). https://arxiv.org/abs/2407.12843
9. **SportsGPT** – "SportsGPT: An LLM-driven Framework for Interpretable Sports Motion Assessment and Training Guidance" (Dec 2025). https://arxiv.org/html/2512.14121v1
10. **BiomechGPT** – "BiomechGPT: Towards a Biomechanically Fluent Multimodal Foundation Model for Clinically Relevant Motion Tasks" (May 2025). https://arxiv.org/abs/2505.18465
11. **LLM-SPTRec** – "Knowledge-grounded large language model for personalized sports training plan generation" (Nature Scientific Reports 2026). https://www.nature.com/articles/s41598-026-37075-z

### Scoping reviews and methodology

12. "Evaluation Strategies for Large Language Model-Based Models in Exercise and Health Coaching: Scoping Review" (JMIR 2025;27:e79217). https://www.jmir.org/2025/1/e79217 · https://pmc.ncbi.nlm.nih.gov/articles/PMC12520646/
13. "Using Large Language Models to Enhance Exercise Recommendations and Physical Activity in Clinical and Healthy Populations: Scoping Review" (JMIR Medical Informatics 2025;13:e59309). https://medinform.jmir.org/2025/1/e59309 · https://pmc.ncbi.nlm.nih.gov/articles/PMC12133071/
14. "Large Language Models in Sport Science & Medicine: Opportunities, Risks and Considerations" (arXiv:2305.03851, May 2023). https://arxiv.org/abs/2305.03851

### Program-generation studies

15. "A professional assessment of training plans for muscle hypertrophy and maximal strength developed by generative artificial intelligence" (2025). https://pmc.ncbi.nlm.nih.gov/articles/PMC12492345/
16. "Reproducibility and quality of hypertrophy-related training plans generated by GPT-4 and Google Gemini as evaluated by coaching experts" (2025). https://pmc.ncbi.nlm.nih.gov/articles/PMC11963122/
17. "AI Hypertrophy Coaching with Wearable Sensors" (arXiv:2512.11854, Dec 2025). https://arxiv.org/pdf/2512.11854
18. "Comparative evaluation of ChatGPT versions in training program design: scientific approach, accuracy, and practical applicability" (2025). https://pmc.ncbi.nlm.nih.gov/articles/PMC12797729/
19. "Exploring Large Language Model as an Interactive Sports Coach: Lessons from a Single-Subject Half Marathon Preparation" (arXiv:2509.26593, 2025). https://arxiv.org/html/2509.26593v1
20. "ChatGPT Outperforms Personal Trainers in Answering Common Exercise Training Questions" (2025). https://pmc.ncbi.nlm.nih.gov/articles/PMC12912680/
21. "GPT-4 as a virtual fitness coach: a case study assessing its effectiveness in providing weight loss and fitness guidance" (2025). https://pmc.ncbi.nlm.nih.gov/articles/PMC12261634/

### Physiotherapy / rehab / safeguarding

22. "Physio: An LLM-Based Physiotherapy Advisor" (arXiv:2401.01825). https://arxiv.org/abs/2401.01825
23. "Large language models in physical therapy: time to adapt and adept" (Frontiers Public Health 2024). https://www.frontiersin.org/journals/public-health/articles/10.3389/fpubh.2024.1364660/full
24. "AI-Powered Physiotherapy: Evaluating LLMs Against Students in Clinical Rehabilitation Scenarios" (MDPI Appl Sci 2025). https://www.mdpi.com/2076-3417/16/3/1165
25. "Toward intelligent clinical support for personalized sport training rehabilitation via large language models" (Springer 2025). https://link.springer.com/article/10.1007/s13755-025-00416-9
26. "How accurately do large language models interpret sport safeguarding principles: an evaluation using the International Olympic Committee framework" (BMC Sports Sci Med Rehab 2026). https://link.springer.com/article/10.1186/s13102-026-01559-2

### Nutrition

27. "Evaluation of LLMs accuracy and consistency in the registered dietitian exam through prompt engineering and knowledge retrieval" (Sci Reports 2024). https://www.nature.com/articles/s41598-024-85003-w
28. "Performance Evaluation of 3 Large Language Models for Nutritional Content Estimation from Food Images" (2025). https://pmc.ncbi.nlm.nih.gov/articles/PMC12513282/
29. "FoodSky: A food-oriented large language model that can pass the chef and dietetic examinations" (ScienceDirect 2025). https://www.sciencedirect.com/science/article/pii/S2666389925000820
30. "Large language models in food and nutrition science: Opportunities, challenges, and the case of FoodyLLM" (ScienceDirect 2025). https://www.sciencedirect.com/science/article/pii/S2665927126000511
31. "When LLMs Can't Help: Real-World Evaluation of LLMs in Nutrition" (arXiv:2511.20652). https://arxiv.org/html/2511.20652

### Motion / video / biomechanics

32. "MotionBench: Benchmarking and Improving Fine-grained Video Motion Understanding for Vision Language Models" (CVPR 2025). https://arxiv.org/abs/2501.02955
33. "Video-MME: The First-Ever Comprehensive Evaluation Benchmark of Multi-modal LLMs in Video Analysis" (CVPR 2025). https://github.com/MME-Benchmarks/Video-MME
34. "MotionLLM: Understanding Human Behaviors from Human Motions and Videos" (2024). https://arxiv.org/html/2405.20340v1

### Commercial and popular-press

35. OpenAI case study – WHOOP / WHOOP Coach. https://openai.com/index/whoop/
36. Google Research blog – "Advancing personal health and wellness insights with AI". https://research.google/blog/advancing-personal-health-and-wellness-insights-with-ai/
37. Nature Medicine – "Improving AI coaching with Gemini using real-world Fitbit data" (2025). https://www.nature.com/articles/s41591-025-03988-x
38. the5krunner – "Fitbit AI Coach: The LLM Shift That Leaves Garmin and Strava Behind" (2025). https://the5krunner.com/2025/10/28/fitbit-ai-coach-llm-shift-garmin-strava-lag/
39. Time magazine – "I Used ChatGPT as My Personal Trainer. It Didn't Go Well". https://time.com/6958557/chatgpt-workout-plan/

### Hugging Face community datasets (training-only, not benchmarks)

40. `onurSakar/GYM-Exercise`, `hammamwahab/fitness-qa`, `its-myrto/fitness-question-answers`, `Varick/workout-routine`, `syncora/fitness-tracker-dataset`, `chibbss/fitness-chat-prompt-completion-dataset`, `dexaai/huberman_on_exercise`. All at https://huggingface.co/datasets.

---

## Appendix A – One-line summaries for quick recall

| Paper | One-liner |
|---|---|
| SportQA | 70K MCQs on 35 sports, 3 levels; GPT-4 scores 23% on hard multi-hop vs. human 92% |
| SPORTU | 900 text MCQs + 12K video QAs; GPT-4o at 71%, Claude 3.5 at 52.6% on hard video |
| SportR | 4.8K images + 2K videos + 6.8K human CoT annotations; SFT+RL barely moves baseline |
| Health-LLM | 10 wearable-prediction tasks across mental/activity/metabolic/sleep on 4 public datasets |
| PH-LLM | Nature Medicine. 857 Fitbit case studies. **Beat human experts 88% vs 71% on fitness MCQs** |
| HealthSLM-Bench | 9 SLMs on 8 wearable tasks across 3 datasets; SLMs ≈ LLMs with efficiency gains |
| PHIA | Personal Health Insights Agent uses code generation; ~4K insight questions |
| NutriBench | 11,857 meal descriptions from 11 countries; GPT-4o beats professional nutritionists on carbs |
| SportsGPT | MotionDTW + KISMAM + 6B-token SportsRAG; beats Claude 4.5 Opus on motion analysis |
| BiomechGPT | Multimodal biomech model trained on 30h motion capture from 500 participants |
| JMIR scoping review e79217 | **20 studies, median rigor 2.5/5, 55% low-rigor – the most important meta paper** |
| JMIR scoping review e59309 | 11/598 studies; LLMs fall short of ACSM standards; calls for a benchmark framework |
| Hypertrophy plans (PMC12492345) | 27-criteria Likert rubric; GPT-4 via Copilot > Gemini > GPT-3.5; all < human experts |
| Half-marathon case study | 2-month single-subject; LLM helped but lacked sensors, safety, real-time feedback |

---

*End of Part I. Continue to [Part II – The fitness industry angle](part-2-industry-frame.md) for the B2B framing, Qodeca context, and fitbench v2 proposal.*
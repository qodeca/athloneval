# Part II – The fitness industry angle

*Part 2 of 3 of the fitness LLM benchmarks research report. Landing page and executive summary live in [`../fitness-llm-benchmarks-research.md`](../fitness-llm-benchmarks-research.md). The academic literature survey that precedes this part is [`part-1-literature-survey.md`](part-1-literature-survey.md). The methodology backbone for the proposed benchmark is [`part-3-benchmark-design-methodology.md`](part-3-benchmark-design-methodology.md).*

*This extension reframes the report around the fitness industry itself – chains, vendors, members, and operators – rather than purely academic research. It is written with Qodeca's business context in mind: a Warsaw-based software house that designs and ships digital products for fitness, sport, and healthcare operators, and that is in a position to either adopt, extend, or create a benchmark as a delivery asset.*

---

## Chapter 11 – Why this angle matters: Qodeca's context

**Qodeca** (qodeca.com) is an IT advisory and custom software development firm headquartered in Warsaw, Poland. It has ~70 employees, 10+ years of history, and a hard specialisation in fitness, sport, and healthcare. Its stated service lines include tech advisory, custom software development, mobile apps (Swift / React Native), product design, MVP development, payment integration, reservation and membership systems, customer engagement, marketing and sales solutions, and **AI solutions and digital transformation**.

Known (publicly referenced) clients include:

- **Bay Club Company** – 30+ campuses, digital transformation, mobile apps
- **SC Fitness** – strategic tech audit
- **PureGym Arabia** – regional gym operator
- **EōS Fitness** – US low-cost chain
- **Solinca** (Portugal) and **BodyMasters** (Middle East) – regional chains
- **eGym** – German connected-strength equipment and software company (appears as a Qodeca client)
- **ABC Glofox** – fitness management software (also Qodeca partner)
- **Jazzercise** – global group-fitness franchise
- **Pro Club**, **Gloveworx** – boutique / destination clubs
- **MedSimples** – healthcare staff management

That client list is unusually aligned with the questions this report explores. Qodeca isn't a generic software house that happens to have a fitness case study – it *delivers into* the exact operators who would pilot LLM features and who need a way to evaluate them before adopting. A well-scoped Athlon Eval would function on at least four layers for Qodeca:

1. **Pre-sales asset** – "we have built and published the industry's reference benchmark for LLM-based fitness coaching; here's how your vendor's model scores on it."
2. **Vendor-selection tool** – per client, pick between GPT-4o / Claude / Gemini / open-weight alternatives based on measured task performance, not marketing.
3. **Delivery QA gate** – a regression harness every new Qodeca-built feature crosses before shipping to a chain.
4. **Thought-leadership and IP moat** – a publicly cited benchmark puts Qodeca in the same sentence as SportQA / PH-LLM / Keepme / Technogym when fitness-tech procurement happens.

This is what changes about the rest of the report: the benchmark can't only be an academic reference – it must also be a **commercial-grade evaluation tool** whose task taxonomy mirrors the workflows Qodeca already ships.

---

## Chapter 12 – The fitness industry landscape (structural view)

### 12.1 The operator tiers

The global health-and-fitness industry is currently served by roughly six operator archetypes, each with different LLM needs:

| Archetype | Examples | Scale | LLM pain points |
|---|---|---|---|
| **Hyperscale low-cost chains** | Basic-Fit (NL/EU), Planet Fitness (US, 19.7M members), PureGym (UK/Arabia), McFit, EōS Fitness | Millions of members per brand; thousands of sites | Volume-intensive member support; cancellation handling; multilingual at scale; fraud and abuse detection |
| **Mid-market full-service chains** | Bay Club, SC Fitness, Solinca, Zdrofit (PL), Calypso (PL) | 10k–500k members; 10s–100s of sites | Personalised program generation at scale; upselling PT; retention outreach; facility Q&A |
| **Boutique / premium** | Orangetheory (post-merger 7k locations with Anytime Fitness), Equinox, SoulCycle, 1Rebel | High-ARPU members; class-based model | Class/coach recommendation; post-class reflection; targeted challenges; community content |
| **Franchise networks** | Anytime Fitness, F45, Curves, Jazzercise | Many low-staff sites | Zero-staff 24/7 support; standardised-yet-local assistant experience; franchisee enablement |
| **Corporate and hospital wellness** | Employer on-site gyms, insurance-funded programs, hospital fitness centres | Large population with health constraints | Medical-grade safety; benefit eligibility; regulatory compliance (HIPAA / GDPR); outcome tracking |
| **Equipment-led ecosystems** | Technogym Mywellness, eGym, Life Fitness/Hammer Strength, Precor | Equipment-centric but increasingly platform-focused | Prescribing machine-specific programs; equipment-aware coaching; cross-device progression |

Qodeca touches five of the six today. That breadth is important for benchmark design: any credible Athlon Eval needs to produce scores that are *actionable for a buyer in each archetype*.

### 12.2 The platform layer

Under the operators sits a smaller set of platforms that intermediate between members and the chain – this is where most LLM deployment actually happens:

| Platform | Role | LLM/ML status (late 2025 / early 2026) |
|---|---|---|
| **Technogym Mywellness / Mywellness Copilot** | CRM + equipment platform, 200+ integration partners | "Technogym Coach" AI, automated program prescription based on user profile + club coaching style. Claims +30% efficiency, +20% retention, +100% upselling. AI-based open platform framing. |
| **Mindbody** (plus Messenger[ai]) | Wellness-business management; powers 40k businesses | GPT-4-powered AI Assistant for software support; Messenger[ai] handles missed-call text-back; **Clients At Risk** ML retention model; **Big Spenders** ML high-value model |
| **ABC Fitness** (Evo, Ignite, Trainerize, **Glofox**) | Multi-brand gym-management stack | Glofox "At Risk" ML report; Trainerize is a PT-facing app with growing AI content features |
| **Keepme** | Dedicated AI retention platform | First ML-centric retention platform for fitness; integrates with Glofox, Xplor Legend, HubSpot; predicts churn and prescribes interventions |
| **Xplor Legend** | Leisure-facility management (payments + membership) | Integrates with Keepme and others; ML applied to retention |
| **eGym** | Connected strength equipment + Fitness Hub | Body analysis, flexibility tests, training progress visualisation; less text-LLM so far |
| **Club Automation** (Daxko) | Mid/large health-club ops | 2026 "AI-powered guide for growing fitness clubs" positioning |

Two observations for Athlon Eval:
- The operator layer is where **decisions** are made (what to buy, what to ship). The platform layer is where **LLMs actually execute**. A good benchmark serves both: vendors compete on scores, operators compare options.
- None of the named platforms publishes a task-level quality benchmark. Most claim business-outcome metrics (retention %, revenue lift) without measurable model-quality evidence. That is a hole a public benchmark can fill.

### 12.3 The AI-native challenger vendors

A swarm of late-stage-startup vendors have moved into fitness AI in 2024–2026, mostly targeting single use-cases:

| Vendor | Use case | Stack |
|---|---|---|
| **Spurfit / Spurfit Copilot** | PT program generation + client management | Hosted LLM, trainer-facing |
| **AI Fitcopilot** | Hypertrophy/strength program design | Hosted LLM |
| **CatalysFit** | Periodised program generation, plateau detection | Hosted LLM |
| **Trainwell** | Human-in-the-loop remote training with AI augmentation | Mixed |
| **AgentiveAIQ, WizFit.ai, MaxMembers.ai, Replify** | Sales / lead conversion / tour booking | LLM agents |
| **GymNation (via LlamaIndex)** | In-house case study: Albus chatbot + Jenny voice agent | RAG + multi-channel |
| **Kaily (GymyBot), Robofy, ChatBot.com gym templates** | FAQ and onboarding chatbots | LLM or template-based |

These vendors compete on features but lack a shared evaluation standard. Again – opportunity.

### 12.4 Industry economics relevant to benchmarking

The benchmark has to *move numbers that operators care about*. The most-cited KPIs in the literature and trade press:

| Metric | Industry reference point | Source |
|---|---|---|
| Annual member retention | 71.4% average; 80% at PT studios | IHRSA |
| Average member tenure | 4.7 years | IHRSA |
| 6-month new-member attrition | ~50% | IHRSA-cited |
| Lead-to-member conversion | 12–20% typical, 50–65% top performers | trade data |
| Free trial to paid conversion | 20–30% typical, 50–65% top performers | trade data |
| Positive staff interactions effect | 2 per month → +1 visit next month | IHRSA |
| Visit → retention effect | Each extra visit cuts cancellation risk 33% | IHRSA |
| AI-driven conversion-rate uplift (best cases) | 1.5% → 60.61% (EMEA case study); GymNation 87% chat conversation rate vs. 60–70% industry | vendor case studies |
| Corporate wellness AI consumer distrust | 47% of users report inaccurate / irrelevant AI outputs | industry press |

Any benchmark track that cannot show a plausible linkage to one of these business metrics is unlikely to be paid attention to by operators.

---

## Chapter 13 – What fitness operators actually do with LLMs right now

Synthesising the deployments found in this research, LLMs in fitness operators today cluster into nine concrete workflows. Each is a candidate Athlon Eval track.

### 13.1 Member-facing workflows

1. **Prospect chat + tour booking** (e.g. GymNation Albus) – WhatsApp / web chatbot answers pricing, hours, facilities, class timings, books a tour. Success metric: chat-conversation rate, tour-book rate.
2. **Onboarding interview + first program** (GymNation, Technogym Copilot, Spurfit) – conversational goal elicitation → first 2–4 week program output. Success metric: plan quality rubric + week-1 adherence.
3. **24/7 member concierge** (Mindbody Messenger[ai], ABC chatbots) – FAQ, cancellations, schedule changes, billing, facility questions. Success metric: deflection rate, resolution accuracy, escalation correctness.
4. **Personalised engagement outreach** (Keepme, Mindbody Clients At Risk, Glofox At Risk) – LLM-written nudges/emails/SMS based on predicted churn or behavioural change. Success metric: reply rate, retained-visit rate, unsubscribe rate.
5. **Conversational coach** (WHOOP Coach, Fitbit AI Coach, Basic-Fit app coach features) – chat with the member about their workouts, suggest next session, explain rationale. Success metric: longitudinal adherence, user satisfaction, safety flags.
6. **Class / content recommendation** (Boutique chains, Mywellness Copilot) – "given your history and goals, book these three classes this week." Success metric: book-through rate, repeat-class rate.

### 13.2 Staff-facing workflows

7. **PT program-generation copilot** (Spurfit, AI Fitcopilot, CatalysFit, Mywellness Copilot, Trainerize) – trainer describes client, LLM drafts full session/week/cycle; trainer edits. Success metric: trainer time saved, edit distance, safety errors avoided.
8. **Client-notes and progress narratives** – trainer or front-desk converts raw numbers into human-readable progress summaries, social posts, check-in messages. Success metric: factual accuracy, tone appropriateness.
9. **Sales assist** (Jenny AI, MaxMembers.ai, AgentiveAIQ) – LLM coaches salespeople on objection handling, drafts follow-ups, proposes next-best-action. Success metric: conversion lift.

### 13.3 Operator-facing workflows (emerging)

10. **Retention-risk interpretation + intervention drafting** – turning a churn-risk model's prediction into a plain-language action plan for a club manager. Success metric: manager-actioned rate, intervention outcome.
11. **Marketing copy at scale** – offers, challenges, social posts, newsletters, local promotions. Success metric: engagement lift, brand-voice fidelity.
12. **Franchisee enablement** – standardising quality of service across 1,000s of franchised sites via an LLM assistant that embodies the brand's playbook. Success metric: playbook-adherence audit.

None of these has a public benchmark.

---

## Chapter 14 – Industry-specific evaluation gaps

Mapping Part I's academic survey onto Chapter 13's operator workflows exposes the gaps cleanly:

| Industry workflow | Nearest existing benchmark | Gap severity |
|---|---|---|
| Prospect chat + tour booking | Generic customer-support benchmarks (Cobbai, etc.) | **Large** – none fitness-specific |
| Onboarding interview + first program | PH-LLM case studies (closed); hypertrophy-plan studies | **Large** – no standardised rubric |
| Member concierge / FAQ | General CS evals | **Large** – no fitness corpus |
| Engagement outreach writing | Marketing-copy evals | **Very large** – no retention-outreach benchmark |
| Conversational coach | PH-LLM, half-marathon case study, JMIR scoping reviews | **Medium** – academic work exists but not open/reusable |
| Class / content recommendation | RecSys benchmarks | **Large** – no fitness-class corpus |
| PT program generation | Hypertrophy-plan studies, SportsGPT, LLM-SPTRec | **Medium** – some work, highly fragmented |
| Progress narratives | General summarisation benchmarks | **Large** – no fitness-narrative corpus |
| Sales assist / objection handling | None | **Very large** |
| Retention-risk interpretation | None | **Very large** |
| Marketing copy at scale | General copy benchmarks | **Medium** |
| Franchisee enablement / playbook adherence | None | **Very large** |

**Synthesis**: Of the twelve real-world LLM workflows in the fitness industry, **ten have no dedicated public benchmark at all**. The two that overlap partially with existing work (conversational coach, PT program generation) are served by fragmented academic studies that no one has consolidated.

### 14.1 Cross-cutting requirements the fitness industry adds

Beyond individual tasks, the industry imposes constraints that academic benchmarks ignore:

1. **Regulatory and privacy posture**
   - **GDPR** for EU operators (Qodeca's core geography) – personal health data is a special category
   - **HIPAA** for any US operator touching insurance-reimbursed members or corporate wellness
   - Generic OpenAI ChatGPT is **not** HIPAA-compliant out of the box; de-identification and Business Associate Agreements required
   - Models must support on-prem, private VPC, or controlled-environment deployment
   - **Benchmark implication**: Athlon Eval must be runnable on self-hosted and vendor-hosted models alike, and must include PII-leakage and prompt-injection-safety tracks

2. **Brand-voice fidelity**
   - A low-cost chain and a luxury club both want an LLM "in their voice"
   - Current benchmarks don't measure brand adherence at all
   - **Benchmark implication**: include a style-adherence task with multiple reference personas

3. **Multilinguality**
   - Basic-Fit operates across the Netherlands, Belgium, France, Spain, Germany, Luxembourg; PureGym Arabia works in Arabic/English; Solinca in Portuguese; Zdrofit/Calypso in Polish
   - Most fitness LLM research is English-only
   - **Benchmark implication**: Athlon Eval should include at least EN, PL, ES, FR, DE, PT, AR language variants for core tasks

4. **Safety-critical escalation**
   - An LLM that says "chest pain is normal during cardio" must hard-route to a human
   - The IOC safeguarding benchmark (BMC 2026) is the only adjacent work
   - **Benchmark implication**: include medical red-flag, abuse-disclosure, and mental-health-crisis escalation tracks with deterministic gold answers

5. **Factual grounding against club-specific state**
   - Facility hours, pricing, class schedules, trainer availability, pool status are *live* data
   - The benchmark must test RAG fidelity, not just model knowledge
   - **Benchmark implication**: include a retrieval+generation track where a corpus of club-specific facts is provided and the model must cite them without hallucination

6. **Latency and cost**
   - A WhatsApp agent that answers in 15 seconds loses the lead; an LLM call that costs €0.30 per conversation is unprofitable at scale
   - **Benchmark implication**: report cost-per-task and p50/p95 latency alongside quality for every benchmark entry

7. **Reproducibility across restarts**
   - Hypertrophy-plan reproducibility work (PMC11963122) showed GPT-4 and Gemini produce materially different plans for the same prompt across runs
   - In a B2B context, this is a compliance and brand-risk problem
   - **Benchmark implication**: include run-to-run consistency scoring

---

## Chapter 15 – Designing Athlon Eval (industry-flavoured)

Taking Part I's three-track academic proposal (Knowledge, Program Generation, Safety) and layering the industry requirements above gives a richer structure. **Still framed as a proposal for discussion – not a decided plan.**

### 15.1 Scope: seven tracks

| Track | What it tests | Primary consumer |
|---|---|---|
| **A. Knowledge** | Exercise science, sports physiology, contraindications, nutrition basics, club operations terminology – MCQ format, multilingual | Academic baseline; vendor comparison |
| **B. Program generation** | Periodised plans for stated goal + population + equipment; automated ACSM/NSCA grounding check + expert rubric + reproducibility | PT copilot vendors; chain programs |
| **C. Member conversation** | Open-ended coach dialogues across goal elicitation, rationale, adaptation, motivation – scored on task success, factual accuracy, tone, safety | Member-app platforms |
| **D. Operational support** | Club FAQ, cancellations, billing, scheduling – grounded in a synthetic club knowledge corpus; deflection and escalation accuracy | Chain support teams; Mindbody-class platforms |
| **E. Retention outreach** | Given a churn signal profile, draft personalised outreach that is empathetic, accurate, and policy-compliant | Keepme-class retention tools |
| **F. Safety & compliance** | Red-flag detection, medical escalation, contraindication awareness, PII-leakage resistance, prompt-injection robustness, safeguarding principles | Everyone; the go/no-go gate |
| **G. Sales assist** | Objection handling, tour booking, lead qualification – grounded in brand-specific playbook | Sales-assist vendors |

Each track produces a numeric score plus sub-dimensions, in the style of HELM / BIG-Bench, and a **cost+latency table**. Results are published on a leaderboard that tracks frontier, open-weight, and fitness-specialised models side-by-side.

### 15.2 Methodological principles (consolidated from Part I + Part II)

1. **Evidence-grounded** – RAG over ACSM, NSCA, WHO, EuropeActive standards plus a synthetic club-operations knowledge base
2. **Reproducible** – all prompts, corpora, model outputs, and human ratings published; versioned like software
3. **Multi-dimensional** – objective checks + expert-rubric scoring + business-proxy metrics
4. **Rigor-aware** – self-scored on the JMIR Evaluation Rigor Score; aim for ≥4/5
5. **Privacy-first** – every example synthetic or consented-and-anonymised; no real PII
6. **Multilingual** – EN, PL, DE, ES, FR, PT, AR minimum for Tracks C–E
7. **Self-hostable** – runnable offline against any HTTP-compatible LLM endpoint
8. **Cost-transparent** – every score paired with dollar cost and latency quantile
9. **Brand-voice aware** – at least 3 reference personas (low-cost value, premium wellness, boutique group-fitness)
10. **Safety-gated** – Track F is a hard filter; models below threshold don't qualify for overall ranking

### 15.3 Construction plan (staged, minimal-loss-if-we-stop-early)

**Stage 1 (MVP – ~2 months)**
- Track A: 500–1000 multilingual MCQs covering exercise science, ACSM FITT, contraindications, chain operations basics
- Track F: 100–200 red-flag scenarios with deterministic gold labels
- Track D: 200 synthetic club-operations QA with a reference corpus
- Leaderboard covering GPT-4o, Claude 3.7/4.5, Gemini 2.x, DeepSeek-V3, Llama 3.3, Qwen 3 – at minimum
- Publish as a paper and a reproducible GitHub repo

**Stage 2 (extended – months 3–6)**
- Track B: 50–100 program-generation scenarios with ACSM-grounded rubric and a small expert panel
- Track C: 200 multi-turn member-conversation scenarios with LLM-as-judge + expert spot checks
- Track E: 100 retention-outreach scenarios with gold-style references from human marketing writers

**Stage 3 (industrial – months 6–12)**
- Track G: sales-assist scenarios with simulated buyer personas
- Integration with a real chain (one of Qodeca's clients) for longitudinal deployment validation – this is what no public benchmark currently has
- Public leaderboard with quarterly refreshes

**Stage 4 (moonshot)**
- Video/motion extension using SportsGPT/BiomechGPT lineage for form assessment
- Wearable-time-series extension borrowing Health-LLM/PH-LLM methodology
- RCT-style outcome correlation track

### 15.4 What Qodeca gets at each stage

| Stage | Deliverable | Commercial value |
|---|---|---|
| 1 | Public leaderboard + paper | Immediate positioning; ICML/NeurIPS workshop target; pre-sales asset |
| 2 | Rubrics + rater protocols + expert network | Repeatable delivery-QA gate; consulting revenue stream |
| 3 | Integration with a real chain | First-of-its-kind longitudinal evaluation; anchor case study for every future pitch |
| 4 | Multimodal extensions | Positions Qodeca alongside Technogym/eGym in the AI-native fitness conversation |

---

## Chapter 16 – Strategic positioning options for Athlon Eval

Three distinct positioning choices to pick between. Each has different implications for scope, marketing, and IP.

### Option A – Academic benchmark (SportQA-style)
- **What it is**: Published as a NAACL/EMNLP/CVPR workshop paper. Public GitHub. No commercial product.
- **Pros**: Credibility, citations, free marketing. Low maintenance burden if open-sourced.
- **Cons**: No direct commercial capture. Competitors can use it too.

### Option B – Commercial evaluation service
- **What it is**: Closed leaderboard + paid evaluation-as-a-service for chains and vendors. Qodeca runs the benchmark against a client's candidate model/stack and produces a certification report.
- **Pros**: Direct revenue. Controls narrative. Defensible if the rubric is proprietary.
- **Cons**: Hard to gain legitimacy without an open, peer-reviewed component.

### Option C – Open benchmark + proprietary augmentation (recommended)
- **What it is**: Public open-sourced core (Tracks A, D, F – knowledge, operations, safety) published under a permissive licence. **Private, proprietary extensions** (Tracks B, C, E, G – program generation, conversation, retention outreach, sales assist) used inside Qodeca's delivery engagements.
- **Pros**: Gets both the credibility of open research *and* the commercial capture of a delivery differentiator. Mirrors how OpenAI evals / DeepEval / MLPerf / HELM operate.
- **Cons**: Requires discipline about where the boundary sits; ongoing maintenance of two tiers.

This is the most defensible play for a consultancy of Qodeca's size: own a small, clean public artefact that everyone cites, then capture value in the engagements that use it.

---

## Chapter 17 – Concrete next steps (if you want to move)

Framed as options with tradeoffs rather than a decided sequence. Pick what resonates.

1. **Talk to 3 existing clients (e.g., Bay Club, EōS, one EU chain) and ask**: "If we gave you a scorecard for LLM quality on a fitness-specific task your team runs, which task would you most want scored?" Their answer prioritises Stage-1 tracks.
2. **Prototype Track A in one week** – draft 100 multilingual MCQs, run on 5 frontier + 5 open-weight models via a single Python script, publish the results as a Qodeca blog post. This costs ~€3k in compute + a few person-days and produces an immediate pre-sales asset.
3. **Partner with a Polish university** (Warsaw Medical University, AWF Warszawa – the physical-ed academy) for expert panel credibility on Tracks B and F. Polish sports-science academia is strong and under-tapped for ML collaboration.
4. **Talk to Keepme, ABC Glofox, Mindbody** about incorporating track scores into their vendor comparisons. Any of these adopting the leaderboard as a reference gives Athlon Eval instant industry legitimacy.
5. **Coordinate (don't compete) with academic benchmark authors** – the SportQA and SPORTU teams are approachable via their GitHub repos. A joint "fitness-operations extension to SportQA" paper would save duplicate work and accelerate acceptance.
6. **Publish the Part I literature review** (what you're reading now) as a Qodeca white paper or Medium post. Regardless of whether you build the benchmark, it's already a solid piece of thought leadership.

---

## Chapter 18 – Risks and uncomfortable truths

A benchmark is not an obvious win. Things to watch:

- **Benchmark hacking** – if Athlon Eval gets traction, vendors will fine-tune to it, just like they fine-tune to MMLU. Mitigate by keeping part of the benchmark private and refreshing it annually.
- **Academic vs. commercial tension** – if Qodeca is perceived as running a benchmark to sell its own services, neutrality suffers. Mitigate by putting the public tracks under independent governance or clear CC-BY licensing.
- **Expert time is expensive** – Tracks B and C need human graders. Underfunding the human side is the single most common cause of benchmark failure (JMIR scoping review data). Budget for it explicitly.
- **Real-world-outcome validity risk** – NutriBench's "When LLMs Can't Help" paper showed intrinsic benchmark wins didn't transfer to real deployments. Athlon Eval should commit to a Stage-3 longitudinal validation or be honest about its limits.
- **"This is a research project"** objection from clients – commercial buyers don't care about leaderboards unless they map to KPIs they are measured on. Every track score needs a one-line translation: "a 10-point Track E improvement ≈ X% retention-outreach reply-rate uplift in deployment." Without that mapping the benchmark is academic theatre.
- **Moving target on models** – frontier models ship every 2–3 months. The leaderboard must be cheap to re-run. Script everything.
- **Competition from Google / OpenAI** – if Google decides to open-source a PH-LLM-style fitness benchmark, Qodeca's window closes. Move fast; don't over-scope Stage 1.

---

## Chapter 19 – Bottom line (industry frame)

**Does a useful fitness-industry LLM benchmark exist today?** No. Academic work covers sports knowledge (SportQA/SPORTU), wearable prediction (Health-LLM/PH-LLM), and isolated coaching studies – but nothing tests the operational workflows that chains and their platforms actually run: onboarding, retention outreach, member concierge, PT copilot, sales assist, safety escalation, brand-voice consistency, or multilingual behaviour. Industry vendors publish outcome claims (retention %, conversion %) without model-level quality evidence, and buyers currently have no way to compare.

**Should Qodeca build one?** The conditions are unusually favourable: direct access to operators (Bay Club, EōS, PureGym Arabia, SC Fitness, Solinca, eGym clients), a specialist positioning that makes it credible, a non-trivial gap in the public literature explicitly called out by two 2025 JMIR scoping reviews, and a commercial use (pre-sales + delivery QA) that partially pays for itself. The recommended shape is **Option C – open public core + proprietary augmentation**, with a Stage-1 MVP focused on Tracks A (Knowledge), D (Operational Support) and F (Safety) in multilingual form, shippable within 2 months for modest cost.

**What happens if you don't build it?** Someone else will, within 12–18 months – most likely a combination of the SportQA/SPORTU academic lineage extending into coaching, plus a commercial vendor (Keepme or Mindbody) publishing a self-serving leaderboard. Neither outcome benefits Qodeca's delivery business. The window is now.

---

## Appendix B – Extended reference list (Part II additions)

### Qodeca and direct client context
- Qodeca company site – https://www.qodeca.com/
- Qodeca services (custom software) – https://www.qodeca.com/services/custom-software-development
- Qodeca Clutch profile – https://clutch.co/profile/qodeca
- Qodeca Crunchbase – https://www.crunchbase.com/organization/qodeca

### Industry association and market context
- Health & Fitness Association (formerly IHRSA) – https://www.healthandfitness.org/
- IHRSA AI class – https://www.healthandfitness.org/publications/how-ai-is-changing-the-health-fitness-industry-class/
- IHRSA 2023 Global Report – https://www.healthandfitness.org/publications/the-2023-ihrsa-global-report/
- EuropeActive digital skills report (coverage) – https://www.leisureopportunities.co.uk/news/EuropeActive-digital-artificial-intelligence-AI-fitness-facilities/346210
- IHRSA 2024 HealthTech coverage – https://www.techaheadcorp.com/blog/pioneering-the-future-of-fitness-ihrsa-2024-unveils-healthtech-giants-reshaping-the-industry/

### Operator and platform AI deployments
- GymNation + LlamaIndex case study – https://www.llamaindex.ai/blog/case-study-gymnation-revolutionizes-fitness-with-ai-agents-powering-member-experiences
- GymNation own announcement – https://gymnation.com/blogs/gymnation-transforms-fitness-with-ai/
- Technogym Mywellness AI open platform – https://www.technogym.com/en-INT/stories/mywellness-open-platform/
- Technogym Coach AI / Mywellness CRM – https://www.technogym.com/my/newsroom/mywellness-crm/
- Technogym AI ecosystem (Athletech) – https://athletechnews.com/technogyms-ai-ecosystem-transforms-gyms-into-healthspan-labs/
- Technogym architecture (Athletech) – https://athletechnews.com/technogyms-ai-powered-journey-the-architecture-of-modern-fitness-wellness-and-health/
- Mindbody AI Assistant (GPT-4 support) – https://www.mindbodyonline.com/business/education/blog/ai-assistant
- Mindbody AI features (Athletech) – https://athletechnews.com/mindbody-ai-features/
- Mindbody Messenger[ai] – https://www.mindbodyonline.com/business/messenger-ai
- ABC Glofox At-Risk retention – https://www.glofox.com/blog/how-glofoxs-ai-can-mitigate-member-churn-and-boost-retention/
- ABC Fitness (parent) – https://abcfitness.com/glofox/
- Keepme main site – https://www.keepme.ai/
- Keepme + Glofox integration – https://www.keepme.ai/integrations/glofox/
- Keepme retention article – https://www.keepme.ai/blog/why-its-so-hard-to-convert-gym-leads-into-paying-members-a-deep-dive
- Basic-Fit Speakap chatbot – https://www.speakap.com/en/speakap-customers/basicfit-smart-chatbot-integration
- Club Automation 2026 AI guide – https://www.clubautomation.com/resources/gym-management-software-ai-guide-2026

### AI-native fitness vendors
- Spurfit – https://www.spur.fit/
- AI Fitcopilot – https://www.aifitcopilot.com/
- CatalysFit – https://catalysfit.com/en/ai-personal-trainer
- Trainwell – https://www.trainwell.net
- AgentiveAIQ gym listicle – https://agentiveaiq.com/listicles/top-7-llm-powered-ai-agents-for-gyms-fitness
- Microsoft Copilot for fitness – https://www.microsoft.com/en-us/microsoft-copilot/for-individuals/do-more-with-ai/ai-for-daily-life/ai-personal-trainer-how-to-plan-your-workouts-with-ai

### Industry KPIs and benchmarks (business, not LLM)
- Mirrors Delivered 2025 gym membership statistics – https://mirrorsdelivered.com/blogs/industry-news-trends/2025-gym-membership-statistics-key-insights-trends
- SmartHealthClubs 100 retention statistics – https://smarthealthclubs.com/blog/100-gym-membership-retention-statistics/
- Kilo 2025 retention strategies – https://usekilo.com/what-are-the-latest-proven-retention-strategies-for-gyms-in-2025/
- Wodify lead conversion – https://blog.wodify.com/post/behind-the-numbers-lead-conversion-rate-attendance
- FinancialModelsLab gym KPIs – https://financialmodelslab.com/blogs/kpi-metrics/gym

### LLM customer-support evaluation (cross-domain precedent)
- Cobbai support LLM benchmarking suite – https://cobbai.com/blog/support-llm-benchmarking-suite
- Anthropic "Demystifying evals for AI agents" – https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
- Evidently AI 30 LLM eval benchmarks – https://www.evidentlyai.com/llm-guide/llm-benchmarks

### Compliance and privacy
- HIPAA Journal on ChatGPT compliance – https://www.hipaajournal.com/is-chatgpt-hipaa-compliant/
- TechMagic HIPAA-compliant LLMs guide – https://www.techmagic.co/blog/hipaa-compliant-llms
- Aisera 7 HIPAA-compliant AI tools 2026 – https://aisera.com/blog/hipaa-compliance-ai-tools/
- HIPAA compliance for AI chatbots (PMC) – https://pmc.ncbi.nlm.nih.gov/articles/PMC10937180/

---

*End of Part II. Return to the [landing page](../fitness-llm-benchmarks-research.md) or revisit [Part I – Academic literature survey](part-1-literature-survey.md).*
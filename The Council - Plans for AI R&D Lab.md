Gemini:


## 1. EXECUTIVE VERDICT

The proposed concept is a solid linear pipeline, but it is **epistemologically naive and architecturally dangerous at scale**.

It assumes that processing more sources yields a refined set of "facts." In bleeding-edge AI engineering, there are no universal facts—only **claims bound to specific contexts** (hardware, software version, model generation). If you compress 30 videos into a single "compiled intelligence" object, you commit destructive compression. You will erase the edge cases, hardware constraints, and context that make the intelligence actionable, resulting in a system that confidently hallucinates outdated or mismatched architectural advice.

The proposal describes a Knowledge Base. You must instead build an **Epistemic Engine**—a system that treats knowledge not as static data, but as a continuous integration (CI) pipeline of claims, evidence, and executable validation.

## 2. WHAT WE ARE ACTUALLY BUILDING

We are building a **Context-Aware Evidence Graph and Automated Validation Pipeline**.

It is a decision-support system that operates like a compiler for engineering policy. It takes unstructured, noisy external claims, links them to a rigid internal ontology, isolates them via a strict trust boundary, and compiles them into Architecture Decision Records (ADRs) only after simulated or physical validation on The Institution’s actual hardware.

## 3. WHAT IS RIGHT ABOUT THE CURRENT PROPOSAL

* **Immutable Raw Storage:** Treating raw sources as immutable, time-stamped ledgers is correct.
* **Decision Provenance:** Recognizing the need to trace a decision back to its source is a critical insight that most RAG systems miss.
* **View Rendering over Document Generation:** "Store once as structured data; render many different views" is the exact correct approach to prevent the "giant Markdown book" anti-pattern.
* **Separation of Validation:** Acknowledging that external knowledge requires internal validation before adoption.

## 4. WHAT IS WRONG / MISSING / DANGEROUS

* **Destructive Synthesis (Information Collapse):** The architecture implies extracting "discoveries" and merging them. If three videos say "MTP is fast" and one says "MTP causes OOM on 8GB VRAM," a naive LLM synthesizer will summarize this as "MTP is generally fast," deleting the critical hardware constraint that will crash your 5060 Ti.
* **Temporal Blindness:** The model lacks a mechanism for knowledge decay. A valid configuration for `llama.cpp` in January 2026 is often a fatal configuration in September 2026.
* **Circular Agent Poisoning:** If multiple AI agents read from and write to the same knowledge layer without isolation, Agent A will hypothesize X, Agent B will ingest X as evidence, and Agent C will declare X a validated fact.
* **Missing Context Binding:** The proposal separates "discoveries" from the specific versions and topologies they apply to. A claim without a `<Software, Hardware Version,>` tuple is useless noise.

## 5. RECOMMENDED INFORMATION MODEL

The system must be modeled as a **Claim-Evidence Graph**, not a database of facts.

* **Entity:** A distinct concept (e.g., `prefix_caching`, `LXC`, `vLLM`).
* **Source:** The origin (e.g., YouTube video, GitHub PR).
* **Claim:** An atomic assertion (e.g., "Prefix caching reduces TTFT by 40%").
* **Context Tuple:** The strict boundaries of the claim (e.g., `[Hardware: A100, Software: vLLM 0.4.1, Model: Llama-3-8B]`).
* **Hypothesis:** An Institution-generated proposal derived from one or more claims.
* **Experiment:** Executable code or manual tests designed to validate a Hypothesis on Institution hardware.
* **Validated Policy (ADR):** The final, contextual engineering rule adopted by The Institution.

## 6. RECOMMENDED DATA / KNOWLEDGE STRUCTURE

A polyglot persistence architecture is required to handle the different shapes of this data:

1. **Graph Database (e.g., Neo4j, Memgraph):** The core intelligence layer. Maps the relationships between Entities, Claims, Sources, and Decisions. Crucial for tracing provenance and conflict detection.
2. **Vector Database (e.g., Qdrant, Milvus):** Used *only* for semantic routing and deduplication of incoming raw transcripts and extracted claims, acting as an index for the Graph.
3. **Time-Series Database (e.g., InfluxDB):** To track benchmark telemetry, validation results, and decay metrics over time.
4. **Immutable Event Ledger:** An append-only log of all extractions, agent actions, and state changes to prevent agent-driven data corruption.

## 7. HOW RAW INFORMATION BECOMES INTELLIGENCE

1. **Ingestion & Stamping:** Source is downloaded, transcribed, and stamped with a precise time/date and author metadata.
2. **Atomic Extraction:** An LLM extracts single, independent `Claims` and their associated `Context Tuples`.
3. **Ontology Binding:** Claims are mapped to existing `Entities` in the graph. If no entity exists, a temporary "Candidate Entity" is created.
4. **Conflict & Corroboration Detection:** The graph calculates if the new claim supports, contradicts, or orthogonally expands existing claims for that Entity.
5. **Hypothesis Generation:** If a cluster of claims reaches a threshold of trust and relevance, an agent drafts a `Hypothesis` for The Institution.
6. **Validation:** The Hypothesis undergoes simulated or hardware testing.
7. **Policy Compilation:** Results alter the Institution's active Architecture Decision Records (ADRs).

## 8. HOW CROSS-SOURCE SYNTHESIS SHOULD WORK

Synthesis must be **additive and differential**, never destructive.

When 30 videos discuss prefix caching, the system does not write a single summary. Instead:

* It clusters the 30 `Claims` under the `Entity: prefix_caching`.
* A Synthesis Agent analyzes the cluster to find the **Delta**: *What variables cause these claims to differ?*
* The output is a structural matrix: "Sources 1-20 (using PCIe 4.0) report a 50% speedup. Sources 21-30 (using PCIe 3.0 or constrained VRAM) report OOM errors or latency spikes."
* This matrix becomes the actionable intelligence, preserving the edge cases that define engineering success.

## 9. HOW TEMPORAL CHANGE SHOULD WORK

Knowledge in AI is highly volatile. The system must implement **Epistemic Decay**:

* **Strict Version Pinning:** No claim enters the system without attempting to bind it to a software/hardware version.
* **Time-To-Live (TTL):** Every claim has an automated decay rate based on its domain. A UI framework claim decays in 12 months; a quantization technique claim decays in 3 months.
* **Event-Driven Invalidation:** If a new major release (e.g., `llama.cpp v1.0`) is ingested, the Graph automatically flags all prior `llama.cpp` claims as `[Stale: Requires Re-validation]`.
* **Never Delete, Only Supersede:** Old recommendations remain in the graph as historical context (crucial for rollbacks), but are filtered out of active "current intelligence" views.

## 10. HOW TRUST / EVIDENCE SHOULD WORK

Trust is a calculated, multidimensional vector, not a static score.

**Trust Score = (Source Base Weight) × (Contextual Rigor) × (Corroboration Factor) × (Age Decay)**

* **Source Base Weight:** Official Docs (0.9), Creator with strong track record (0.7), Unknown Creator (0.4).
* **Contextual Rigor:** Did they show a benchmark on screen? Did they share code? (High multiplier). Is it just them talking to a camera? (Low multiplier).
* **Corroboration Factor:** Does the graph show 5 other independent sources making the same claim?
* **Hardware Proximity:** Does their hardware setup match ours? A trusted source on H100s has a *lower* functional trust score for The Institution's 5060 Ti architecture than a medium-trust source running consumer GPUs.

## 11. HOW INSTITUTION VALIDATION SHOULD WORK

This is the system's "Air Gap." External claims—no matter how trusted—are merely hypotheses until they cross this gap.

* **Hypothesis State:** "External consensus suggests Hermes sub-agents should share a unified KV-cache."
* **Experiment Design:** An AI agent writes a test script or Terraform/Ansible playbook to spin up this topology locally.
* **Execution:** The test is run on The Institution's *actual* hardware (e.g., local 5060 Ti, or specific cloud instance).
* **Institutional Truth:** The telemetry from the test dictates the result. The system records this as `Validated_Local_Success` or `Validated_Local_Failure`. External knowledge is subordinated entirely to this result.

## 12. HOW DECISIONS SHOULD BE REPRESENTED

Decisions are represented as **Architecture Decision Records (ADRs)**, which act as the root nodes for AI system prompts.

An ADR node contains:

* **Status:** Proposed, Accepted, Deprecated, Superseded.
* **Context:** Why the decision is needed.
* **Decision:** The exact configuration or architectural choice.
* **Consequences:** Trade-offs accepted.
* **Provenance Chain:** `ADR` ← `Institution Experiment` ← `Hypotheses` ← `Graph of Claims` ← `Raw Sources`.

When an AI worker is spawned, it loads only the `Accepted` ADRs as its system context.

## 13. HOW ACTIONABILITY SHOULD WORK

Beyond the prompted questions, every knowledge object must answer:

* **IS THIS AUTOMATABLY TESTABLE?** (Can we write a script to verify this right now?)
* **WHAT IS THE BLOAT FACTOR?** (Does this add unacceptable complexity to our stack?)
* **HARDWARE DEPENDENCY LEVEL:** (Strict, Loose, Agnostic)
* **VULNERABILITY SURFACE:** (Does this introduce security/isolation risks, e.g., LXC container escapes?)

## 14. HOW THE SYSTEM SHOULD SCALE

At 10,000+ sources, manual curation dies.

* **Automated:** Ingestion, transcription, atomic claim extraction, initial graph linking, TTL decay, conflict detection.
* **AI-Assisted:** Hypothesis generation, experimental code drafting, differential synthesis of claim clusters.
* **Human-Reviewed:** The final approval of an ADR (changing the architecture). Resource allocation for expensive cloud experiments. Core ontology adjustments.
* **Data Pruning:** At scale, vector spaces become crowded. The system must automatically archive "cold" claims (older than 18 months, uncorroborated, zero ADR impact) out of active retrieval into cold storage.

## 15. HOW MULTIPLE AI WORKERS SHOULD INTERACT WITH IT

To prevent hallucination loops and contradictory edits:

* **Git-like Branching:** Workers do not write directly to the main graph. When a research agent works, it operates in a `Knowledge Branch`.
* **Strict Write Permissions:** Only a designated `Curator Agent` (or human) can merge a branch into the main trunk.
* **Provenance Tagging:** Every node and edge must carry the signature of the Agent that created it. If an agent is found to be hallucinating, its entire subtree can be rolled back.
* **Read-Only Context:** Execution agents (coding, building) only have read access to `Validated Policy`, never to raw claims or unverified hypotheses.

## 16. WHETHER / WHEN TO USE RAG

**Naive RAG must be aggressively avoided.** Chunking transcripts, embedding them, and pulling the top 5 similarity hits will return contradictory advice from 2024 and 2026, completely stripping hardware context.

**Use GraphRAG and Structured Querying:**

* AI agents must use tool calls to query the Graph DB via Cypher/SQL to enforce constraints: *"Get claims about prefix_caching WHERE version >= vLLM 0.3.0 AND hardware_class = 'consumer_gpu'"*.
* Only *after* the graph traversal identifies the correct `Claims` and `Sources` do you use vector retrieval to pull the exact raw transcript chunks for nuance.
* *Verdict:* Use structured querying for logic and temporal filtering; use vector retrieval strictly as a fallback for fuzzy entity matching during ingestion.

## 17. WHAT SHOULD BE AUTOMATED

* **The Ingestion Pipeline:** Polling YouTube channels, GitHub repos, and RSS feeds.
* **The Extraction Pipeline:** LLM-driven JSON extraction of claims and context tuples.
* **Graph Maintenance:** Calculating trust decay, flagging stale nodes, computing consensus scores.
* **Validation Execution:** Running benchmark scripts on designated hardware testbeds.

## 18. WHAT SHOULD REQUIRE HUMAN JUDGEMENT

* **Ontology Definition:** Deciding that "Proxmox" and "LXC" are foundational primitives requiring high-weight tracking.
* **ADR Approval:** Human signs off on architectural shifts that affect security, cost, or core capabilities.
* **Trust Calibration:** Humans adjusting the "Base Weight" of specific creators or sources if the AI misjudges their empirical rigor.

## 19. MINIMUM VIABLE VERSION

1. **Storage:** SQLite (for telemetry/ADRs) + local Neo4j instance + local Qdrant instance.
2. **Pipeline:** A simple Python script that pulls video transcripts -> passes to a local LLM to extract JSON `[Claim, Hardware, Software, Date]` -> loads into Neo4j.
3. **Output:** A daily Markdown dashboard generated by querying the graph for "New conflicting claims" and "High-consensus hypotheses."
4. **Validation:** Human reads dashboard and manually tests.

## 20. LONG-TERM VERSION

An autonomous, multi-agent CI/CD pipeline for knowledge.

* **Scraper Agents** monitor the ecosystem.
* **Extractor Agents** build the graph.
* **Synthesis Agents** detect gaps and write experimental scripts.
* **Hardware Agents** provision Proxmox VMs, run the scripts, record telemetry, and tear down.
* **Curator Agents** update ADRs based on telemetry.
* The Institution automatically rebuilds its own infrastructure based on newly compiled ADRs.

## 21. FAILURE MODES WE MUST DESIGN AGAINST

* **The "Attention Economy" Bias:** YouTubers make videos about flashy new models (e.g., Llama-4), not stable API routing. If the system biases toward volume, it will ignore critical foundational primitives. *Fix: Hardcoded ontology weights for infrastructure terms.*
* **Ontology Fragmentation:** Extractors creating 15 different entities for `kv-cache`, `Key-Value Cache`, `KV Caching`. *Fix: Strict LLM tool-calling tied to an enforced list of existing graph entities.*
* **Confirmation Bias Looping:** An internal agent writes an optimization script -> publishes it to GitHub -> ingestor reads it back as "external corroboration." *Fix: Strict tagging of internal vs external origin.*

## 22. CONCRETE RECOMMENDATION

Discard the concept of a "knowledge base" and implement an **Epistemic Graph Database (Neo4j)** wrapped in a strict **Event-Sourced Architecture**.

Force all LLM extractions into rigid JSON schemas (`Claim`, `Context_Tuple`, `Evidence`). Implement automated temporal decay from day one. Build the "Air Gap" validation layer immediately; no external claim should ever be promoted to a "decision" without local telemetry. Focus initial extraction heavily on unglamorous infrastructure primitives (Proxmox, networking, state management) over model flavor-of-the-week updates.

## 23. QUESTIONS WE HAVE NOT ASKED BUT SHOULD

1. **How does the system "unlearn"?** When an architectural paradigm shifts entirely (e.g., transformers to state-space models), how do we cleanly archive the obsolete subtree without corrupting historical decision provenance?
2. **What is the schema for an executable experiment?** If AI is to validate claims automatically, what standard format (Ansible, Terraform, Python test suites) will it use to interface with The Institution's hardware?
3. **How do we handle multi-modal evidence?** When a video shows a terminal screen with a critical OOM error, but the transcript doesn't mention it, how do we integrate VLM screenshot extraction into the ingest pipeline without exploding costs?






Kimi:

# THE INSTITUTION — COUNCIL REVIEW RESPONSE

## 1. EXECUTIVE VERDICT

The proposed concept is directionally correct and, in its stated principles, already wiser than most systems that get built. But it contains one central conceptual error that will, if uncorrected, reproduce the exact failure it fears — a giant, stale, human-dependent knowledge mass.

**The error: "knowledge objects" are proposed as stored, maintained artefacts.**

A "knowledge object" that is a paragraph of structured prose sitting in a database is a summary with extra steps. If your team (human or AI) must *manually maintain* synthesis objects — updating each one when new evidence arrives — then at 1,000 sources the system will be permanently out of date, and at 10,000 it will be a liability. The history of knowledge management is a graveyard of curated knowledge bases that rotted faster than they were maintained.

The corrected architecture, in one sentence:

**Store only two things durably: immutable evidence (quote-anchored, timestamped) and immutable decisions. Everything in between — knowledge, synthesis, assessments, briefings — is a *derived, regenerable, versioned computation* over the evidence, never a hand-maintained artefact.**

A second, sharper verdict on the funnel: the 10→100→40→15→8→5→3 cascade is a seductive pipeline fantasy. Value does not concentrate monotonically. One verbatim sentence from one video ("llama.cpp MTP now works with quantised KV cache") can outweigh forty knowledge objects. Designing the system around a narrowing funnel invites metric gaming: extractors will be optimised to produce funnel-friendly output, not truth. Design around **claims and traceability**, not funnel counts.

## 2. WHAT WE ARE ACTUALLY BUILDING

Strip the terminology away and this is: **an institutional engineering decision ledger with an evidence spine.**

It is not a knowledge graph (traversal is a means, not the product). It is not a RAG system (retrieval is a view, not the store). It is not an ontology (the domain shifts too fast for a fixed schema of types). It is not a wiki or a notebook.

The closest existing analogues are:
- an **intelligence-analysis pipeline** (raw reporting → evaluated claims → assessments with confidence and sourcing),
- an **ADR (Architecture Decision Record) ledger** extended backwards to evidence,
- and a **scientific lab notebook** for your own experiments.

Call it what it is: an *evidence-and-decision system*. Every requirement in the brief — synthesis, decay, trust, provenance, validation — falls out naturally from that framing, and several requirements in the brief (RAG, ontology, knowledge graph) are exposed as optional implementation details rather than load-bearing concepts.

## 3. WHAT IS RIGHT ABOUT THE CURRENT PROPOSAL

- **Provenance-first instinct.** Requiring traceability back to evidence is the single most important property, and the proposal has it.
- **"The video is the source; the discovery is the unit of intelligence."** Half right — the discovery should be an *atomic claim*, but the instinct (separate source from extracted intelligence) is correct.
- **"Store once as structured data; render many views."** Correct, and it should be applied more aggressively than proposed (see §4).
- **The pipeline shape** (evidence → knowledge → hypotheses → validation → decisions) correctly identifies that adoption is a *gate*, not a default.
- **The three-tier boundary** (general knowledge vs. Institution hypothesis vs. validated knowledge) — correct and crucial.
- **Temporal awareness** and **attention to boring architectural primitives** — both are signs of genuine engineering maturity.
- **Compiled intelligence as a product** — correct, and should be promoted from "possible view" to "the primary output of the system" (see §4).

## 4. WHAT IS WRONG / MISSING / DANGEROUS

**a) Mutable synthesis objects are the core design flaw.** Any artefact that a human or agent *edits in place* — updating a prefix-caching synthesis when video 31 arrives — is unmaintainable at scale and destroys auditability ("what did we believe on June 3rd?"). Syntheses must be regenerated from claims by explicit procedures, versioned, and treated as caches.

**b) No atomic unit of falsifiable content.** "Observation," "discovery," and "knowledge object" are undefined sizes. Without a *claim* — a single, atomic, dated, conditioned, source-anchored, potentially-false assertion — you cannot do contradiction detection, deduplication, clustering, confidence reasoning, or invalidation. Claims are to this system what transactions are to a ledger.

**c) No representation of contradiction as a first-class state.** The brief asks "do they contradict one another?" but the pipeline implies contradictions get resolved. Dangerous. Contradictions with strong support on *both* sides must be representable as a stable, visible state, not averaged into a compromise. Auto-resolution is how misinformation becomes institutional truth: the system smooths genuine disagreement into confident false consensus.

**d) Ontology risk.** Pre-defining types (configurations, primitives, failures, contradictions) will ossify within months. The core schema should be minimal (sources, evidence, claims, entities, assessments, experiments, decisions); everything else should be tags/predicates that can evolve without migration.

**e) Scalar trust scores will rot.** A "0–1 trustworthiness per source" is exactly the "source X is always trustworthy" model the brief forbids, in numerical clothing. Scores get gamed, drift, and convey false precision.

**f) Hallucinated extraction.** An LLM asked to "extract observations" from a transcript will extract things the video never said. Without verbatim quote anchoring and an independent verification pass, the corpus becomes an elegant machine for laundering hallucinations into structured "facts."

**g) Independence collapse / provenance laundering.** Thirty videos on prefix caching may be thirty restatements of one Reddit comment or one PR. If the system counts sources rather than *independent* sources, corroboration is fake and confidence is inflated. This is the single most likely way the system produces confident garbage at scale.

**h) Write-mostly corpus risk.** The brief worries about noise but not about the opposite failure: a beautifully structured corpus that no agent can actually load into context. The compiled intelligence layer is the real product; the rest is substrate.

**i) No concept of scheduled re-evaluation.** "Knowledge decay" is mentioned as if it's a property of data. It's not — decay is a *workflow*. Without a job system that proactively enqueues "this standing assessment is 90 days old and its domain just moved," decay never happens and staleness accumulates invisibly.

**j) The self-improvement loop is drawn without a safety gate.** Experiment → knowledge update → architectural change → new data is fine, but the loop as written has no sign-off point. An autonomous loop that can alter the architecture that runs the loop is a runaway risk (see §21).

## 5. RECOMMENDED INFORMATION MODEL

Three planes, one ledger:

**Plane 1 — Evidence (append-only, immutable).**
Sources (channel, video, URL, publish date, retrieval date, creator, license/terms note) → Segments (timestamped verbatim quote spans, never paraphrased). Nothing here is ever edited or deleted. Paraphrase is forbidden at this layer; every downstream fact must anchor to characters in a transcript.

**Plane 2 — Claims (append-only, immutable).**
An atomic claim: *subject-predicate-object*, timestamped (claim made at T, about software version V), conditioned (hardware, config, workload), linked to ≥1 evidence segment. Claims can be *retracted* (by a later claim linking to it) but never deleted or edited. Retraction is itself a claim. This plane is small, strict, and boring — by design.

**Plane 3 — Judgements (versioned, derived, regenerable).**
Everything else lives here: entities (prefix caching, llama.cpp, Hermes), entity relationships, claim clusters, standing assessments, hypotheses, experiment designs, findings. Every object is stamped with *how it was produced* (which procedure, which model, which prompt version, which claims it consumed) and can be regenerated. Hand edits are transactions, not edits.

**The Decision Ledger (append-only, immutable).**
Institution decisions as ADR-style entries: context, options, claims and assessments relied on (by ID + version snapshot), predicted consequences, reversibility, review-by date, supersession links.

**Key invariant:** Planes 1 and 2 and the ledger only grow. Plane 3 is a cache with provenance. If Plane 3 is corrupted, confusing, or bloated, you wipe and regenerate it — this property alone makes the system robust in a way no curated knowledge base can be.

## 6. RECOMMENDED DATA / KNOWLEDGE STRUCTURE

- **Postgres or SQLite as the core store.** You need ACID integrity constraints, versioning, and arbitrary queries. You do not need a graph database; claim-to-claim adjacency is a join table, and reachability queries are a closure table or a regenerated view. Graph DBs solve a traversal problem you mostly don't have and sacrifice the constraint-checking you desperately do have (duplicate claims, dangling provenance, broken supersession chains are integrity failures, not query inconveniences).
- **JSON payloads** for claim conditions and assessment rationales; relational columns only for the fields you query on (entity, predicate, date, status, cluster).
- **Event-sourced pattern:** every change to Plane 3 is an event; current state is a projection. "What did we believe in March?" is a query, not an archaeology project.
- **Entity registry** as a lightweight, human-curated glossary (canonical names, aliases, "is-a", "version-lineage"). This is one of the few places where human curation pays for itself — entity resolution ("is this video's 'MTP' the same as that one's 'speculative decoding'?") is where automated systems silently corrupt everything downstream.
- **Materialized views** for compiled intelligence: briefs, subsystem briefs, validation queues, recent-changes feeds. These are regenerated by jobs, versioned, and carry a "generated from claims up to ID N" watermark.

## 7. HOW RAW INFORMATION BECOMES INTELLIGENCE

The pipeline, corrected:

1. **Ingest** (automated): metadata, transcript, publish date. Nothing is summarised yet.
2. **Extract** (AI, quote-anchored): candidate claims, each requiring a verbatim transcript span + timestamp. Claims that cannot be anchored are rejected.
3. **Verify** (AI, *independent* — different model/provider than the extractor): check each claim against its anchor. Does the quote actually support the claim? Reject or downgrade.
4. **Resolve** (AI-assisted, human-escalated): map claim subjects/objects onto the entity registry; detect near-duplicate claims (embedding similarity + predicate/condition match) and merge into claim clusters.
5. **Cluster & synthesise** (AI): claims on the same (entity, predicate, conditions) are grouped; a standing assessment is *generated* stating what the cluster currently supports, the spread of evidence, contradictions, and the strongest independent corroboration.
6. **Route** (rule-based + AI): clusters touching Institution components become Institution-relevant; those with adoption implications spawn hypotheses; hypotheses spawn experiment proposals.
7. **Decide** (human-gated): hypotheses → experiments → findings → decisions. This is where general knowledge may become Institution truth — nowhere earlier.
8. **Regenerate** (scheduled): all Plane 3 views and compiled briefs are rebuilt incrementally as new claims land.

Notice what disappeared: humans reading videos, humans maintaining synthesis objects, and any step where information is silently transformed rather than explicitly transformed with provenance.

## 8. HOW CROSS-SOURCE SYNTHESIS SHOULD WORK

Synthesis operates on claim clusters, not on videos. For each cluster the standing assessment must answer, structurally:

- **Are these the same technique?** (entity resolution — is it implemented in llama.cpp, vLLM, a Hermes config, a cloud API?)
- **Are conditions comparable?** (hardware, model, quantisation, workload, version). A claim with mismatched conditions cannot corroborate; it can only *conditionally* support, and the assessment must say under which conditions.
- **Do they reinforce or contradict?** Both directions are recorded with enumerated claim IDs on each side. Contradiction is a stable, queryable state — "UNRESOLVED CONFLICT: claims C-1041..C-1077 vs C-1088..C-1102" — visible in every compiled view that touches the topic.
- **Independence audit:** the assessment explicitly lists how many *independent origins* exist (traced through citation chains). Thirty videos citing one PR = one origin, and the assessment must say so. This is the anti-laundering control.
- **Authority weighting:** official docs/source code > reproducible benchmarks > direct demonstrations > creator statements > anecdotes — but as *evidence-type metadata on each claim*, combined with conditions-match to your hardware, never as a creator-level score.

The assessment then states what it means *for the Institution* and, if applicable, proposes a test. Every standing assessment carries: `generated_at`, `evidence_window` (claim IDs), `last_verified`, `stale_after`, `invalidation_triggers`, and a **mandatory written rationale**. If the rationale can't be written, the assessment isn't ready — no matter what the score says.

## 9. HOW TEMPORAL CHANGE SHOULD WORK

- **Every claim is dated twice:** when it was published and what software/hardware world it describes (versions). Claims are valid *for their stated conditions* — there are no timeless claims about this domain.
- **Entities carry version lineages.** "llama.cpp" is not an entity; "llama.cpp b4xxx" is. Assessments name versions and benchmarks are version-pinned (model, quant, backend, hardware, date) or they don't enter the corpus.
- **Decay is a job, not a property.** Each standing assessment has `stale_after` and `invalidation_triggers`. A scheduler enqueues re-evaluation tasks when (a) staleness hits, (b) a new claim lands in the cluster, or (c) a trigger fires (e.g., "new major llama.cpp release"). Re-evaluation regenerates the assessment; it never silently edits it.
- **Supersession, not deletion.** Old recommendations remain, marked SUPERSEDED with a link to what replaced them and why. "What did we believe in 2026?" must always be answerable — both for audit and because sometimes the old belief becomes right again.
- **Changed creator opinions** are claims too: creator X said A in March, B in August; the cluster holds both; the assessment notes the shift. The system tracks *people changing their minds*, which is some of the highest-value signal in fast-moving domains.

## 10. HOW TRUST / EVIDENCE SHOULD WORK

Per-claim evidence profile, never a source-level scalar:

- **Evidence type:** source code / official docs / reproducible benchmark / direct demonstration / secondhand report / anecdote.
- **Reproducibility:** did the source provide configs/commands? Could we rerun it?
- **Directness:** did the source observe this themselves?
- **Independence:** traced origin; how many distinct origins support this cluster.
- **Recency & conditions-match:** date, plus computed distance to Institution hardware/profile (a quad-3090 result on your 5060 Ti target is tagged `conditions-mismatch:gpu`).
- **Corroboration structure:** count and type of independent corroborating claims.

Confidence on a standing assessment = **coarse ordinal (low/medium/high) + mandatory written rationale + explicit invalidation triggers**. No 0.87. The rationale forces the system (and the humans auditing it) to confront *why* it's confident, which is where laundering gets caught. High confidence with thirty non-independent origins should be impossible to express — the rationale template requires stating the independent-origin count.

## 11. HOW INSTITUTION VALIDATION SHOULD WORK

Strict promotion gates, enforced by schema and workflow, not discipline:

```
EXTERNAL CLAIM → INSTITUTION HYPOTHESIS → [EXPERIMENT] → VALIDATED | REFUTED | DEFERRED
```

- Only claims survive ingestion; nothing external ever becomes VALIDATED without an experiment executed on Institution hardware (or Institution-designated cloud) with a **full config snapshot** (model file hash, backend version, flags, quant, hardware, workload, dates).
- Cloud-provider results and third-party benchmarks may support a hypothesis but carry a permanent `external-conditions` tag; they can never promote it.
- Experiment records are immutable and link hypothesis → procedure → config snapshot → raw results → finding. A finding is a claim produced by The Institution about itself — same atomicity, same anchoring, but its evidence type is "own experiment," the highest type.
- Findings feed back as new claims (closing the loop) — but with `produced_by: institution` and independence handling so that our own prior results don't count as *independent* corroboration of our own new results.

## 12. HOW DECISIONS SHOULD BE REPRESENTED

Append-only ADR entries with:

- Context and forcing function ("agentic workload KV cache growth exceeded 24GB")
- Options considered, each with pros/cons as claims
- **Claims relied on:** assessment IDs + version snapshots — so the decision records *what we knew, as we understood it then*
- Predicted consequences and how we'll know if we were wrong
- Reversibility class (trivial / cheap / expensive / near-irreversible)
- `review_by` date, owner, supersession links

"Why did we choose this?" is then a deterministic walk: Decision → assessments (at that version) → claim clusters → claims → evidence segments → timestamps. No summarised memory of reasoning — the reasoning chain is recomputable. This is the difference between *recording conclusions* and *preserving the ability to re-derive them*.

## 13. HOW ACTIONABILITY SHOULD WORK

Your question list is good. Add three fields — they are the highest-leverage additions:

- **"What would change our mind?"** (invalidation triggers) — makes every belief falsifiable and feeds the decay scheduler.
- **"Cost of being wrong / cost of ignoring"** — routes attention. A wrong belief about prefix caching costs little; a wrong belief about worker isolation or state management can cost the whole architecture. This is how boring-but-critical primitives get disproportionate attention *mechanically*, not by hoping an agent notices.
- **"What does this enable or block?"** — dependency edges between knowledge and architecture. Many small facts matter only because they unblock something large.

Also: every assessment with an action implication must name a **proposed test** (even if deferred). "Do X" without "here's how we'd verify X" is an opinion, not engineering guidance.

## 14. HOW THE SYSTEM SHOULD SCALE

| Layer | 10 sources | 1,000 | 100,000+ |
|---|---|---|---|
| Ingest/extract/verify | manual-ish | automated | automated + sampling audits |
| Dedup/entity resolution | human | AI + human escalation queue | AI, humans handle only escalations |
| Synthesis/assessments | human-reviewed | AI-generated, human spot-audit (sampled, with escalation triggers) | AI-generated, audit by exception |
| Promotions/decisions | human | human | human (this never automates) |
| Views/briefings | on demand | scheduled regeneration | incremental materialized views |

Principles: **curation effort must grow sub-linearly with corpus size or the system is already dead.** Immutability applies to evidence, claims, and decisions; regeneration applies to everything else. Re-evaluation at scale is a *queue of review tasks with priorities*, not a batch rewrite — the system enqueues "look at this" work rather than attempting to "stay correct" globally.

## 15. HOW MULTIPLE AI WORKERS SHOULD INTERACT WITH IT

- **Agents never edit; they propose.** Every agent writes to a proposal/transaction queue. Objects enter the corpus only through typed procedures (extract, verify, merge, assess) that enforce schema, anchoring, and dedup on write. This single rule eliminates corruption, contradictory edits, and uncontrolled growth.
- **Write-time dedup is mandatory.** An agent proposing a claim that near-matches an existing cluster gets routed to the cluster, not inserted.
- **Extractor and verifier must be independent** (different models/providers). Same-model verify is self-confirmation and produces circular reinforcement with extra steps.
- **Every AI-produced artefact carries full provenance:** model, version, prompt/template version, input claim IDs. When you later distrust an extractor, you can identify and regenerate every object it touched. (You should be asking now: *can we?*)
- **Workers consume compiled views and targeted queries, never raw corpus scans.** Retrieval quotas and structured filters prevent context blowup and keep agents on current assessments rather than stale derived text.
- **Agents cannot promote anything.** External-claim → hypothesis → validated transitions require a human gate (in the long term: a human-plus-independent-verifier gate). This is the non-negotiable line against hallucinated institutional truth.

## 16. WHETHER / WHEN TO USE RAG

**From day one — but demoted to what it actually is: an indexing layer over the immutable raw corpus, used to find sources, never to state facts.**

- Structured querying (claims by entity/predicate/date/status) is the primary access path for derived knowledge. RAG over derived knowledge is forbidden as a substitute — it reintroduces unverifiable paraphrase at the layer where precision matters most.
- RAG over raw transcripts is legitimate and useful ("find every source discussing KV cache quantisation under agentic load") — with results always returned as anchored segments, so the consumer verifies against the quote.
- If a hybrid is ever needed (semantic search within structured filters), treat it as a query accelerator on views, not a knowledge store.
- Rule of thumb: **if the answer needs to be true, it comes from structured claims; if the task is to locate, RAG serves.** The moment RAG output enters the corpus without re-anchoring to evidence, the system has begun laundering itself.

## 17. WHAT SHOULD BE AUTOMATED

Ingestion, transcription, quote-anchored extraction, extraction verification, duplicate detection, claim clustering, evidence-type classification, staleness detection, re-evaluation scheduling, compiled briefing generation, experiment-config templating, provenance bookkeeping, integrity checking (dangling anchors, broken supersession chains), and sampling audit selection (choose which AI-produced artefacts humans review — bias selection toward high-impact, high-confidence, and contradictions).

## 18. WHAT SHOULD REQUIRE HUMAN JUDGEMENT

- **Entity disambiguation escalations** (the "is this the same thing?" calls that resist automated resolution)
- **Contradiction adjudication** when both sides have strong, independent support — a machine must never average these away
- **Promotion gates:** hypothesis → experiment → validated; anything becoming Institution truth
- **Decisions and decision reversals**
- **Invalidation calls** on existing validated knowledge
- **Schema and procedure changes** (changes to how the system thinks, not what it thinks)
- **Sample audits** of extraction and assessment quality — ongoing, forever
- **Trust crises:** "this creator/source was wrong en masse" — deciding what to retract or re-evaluate

## 19. MINIMUM VIABLE VERSION

Deliberately small. One database (SQLite or Postgres), one directory of immutable transcripts, seven tables: `sources`, `segments`, `claims`, `claim_links`, `entities`, `assessments`, `decisions` (+ `experiments` if any). One channel ingested. One extractor model + one independent verifier. Claims stored with anchors, dates, conditions. A weekly regenerated **compiled briefing** (this is the product — if it isn't useful at 50 claims, no amount of scale fixes it). One claim-cluster synthesis done by hand to calibrate what the automated version must produce. No graph DB, no ontology work, no UI, no multi-agent anything.

MVP success criteria, stated now: (a) a planning question from the last month can be answered with full source trace in under 5 minutes; (b) the briefing is actually read and acted on; (c) one injected false "fact" is caught by anchoring or verification. If (c) fails, nothing else matters.

## 20. LONG-TERM VERSION

Event-sourced core; Plane 3 as a set of incrementally maintained materialized views; a scheduler fleet for decay and invalidation triggers; an automated experiment runner for *pre-approved* experiment templates (with human sign-off on results entering the corpus); an agent proposal queue with typed procedures; evaluation harnesses that continuously measure briefing precision and provenance integrity; hot views possibly served from specialised stores — as **read replicas only**, never as systems of record. The compiled intelligence layer becomes the primary interface for every Institution agent, with subsystem-scoped views generated per worker.

## 21. FAILURE MODES WE MUST DESIGN AGAINST

1. **Auto-consensus:** contradiction smoothing. Controls: unresolved-conflict as a first-class state; rationale templates that require enumerating both sides.
2. **Extraction hallucination.** Controls: verbatim anchoring, independent verification, sampling audits.
3. **Provenance laundering / independence collapse.** Controls: origin tracing, independent-origin counts in every assessment.
4. **Circular reinforcement:** own experiments confirming own priors counted as independent evidence; same-model extractor/verifier agreeing with itself. Controls: independence tagging, model diversity requirements.
5. **Scalar confidence rot.** Controls: ordinal + mandatory rationale + invalidation triggers.
6. **Write-mostly corpus:** beautiful data nobody can consume. Controls: compiled views as first-class, budgeted, watermark-stamped products with regeneration contracts.
7. **Stale-as-current:** an old assessment served by a retrieval path that ignores `generated_at`. Controls: views must display recency; expired assessments are visually and structurally demoted.
8. **Ontology ossification.** Controls: minimal core schema, tags/predicates for the rest, schema changes as human-gated transactions.
9. **Curation collapse at scale.** Controls: sub-linear curation design, regeneration-over-editing, audit-by-exception.
10. **Funnel gaming.** Controls: evaluate on traceability and decision quality, not extraction counts.
11. **Runaway self-improvement loop:** an agent that can modify the architecture that runs the agents. Controls: the loop is closed only at *experiment proposal*; humans close the loop at adoption. The system may propose its own improvement experiments; it may not run them unsupervised.
12. **Extractor drift:** silently upgrading the extraction model changes the corpus's past. Controls: pinned model versions per procedure; full provenance so affected objects are identifiable and regenerable.
13. **Contradiction between compiled brief and underlying claims** going undetected. Controls: briefings carry claim-ID watermarks; integrity jobs sample brief statements back to claims.

## 22. CONCRETE RECOMMENDATION

Build the **claim spine + decision ledger + compiled briefing**, and treat everything else as derived machinery around them. Specifically:

- Adopt the three-plane model: immutable evidence and claims, derived judgements, immutable decisions.
- Make claims atomic, quote-anchored, dated, conditioned, and retraction-based.
- Make all synthesis regenerable with mandatory written rationales and explicit invalidation triggers; contradictions are a stable, visible state.
- Enforce the three-tier knowledge boundary with human gates at every promotion.
- Use RAG only over raw sources as a finding-aid; structured claims for truth.
- Delay or reject: graph databases, ontologies, fine-grained trust scores, autonomous experiment loops, and any hand-maintained synthesis object.
- Start with the MVP in §19 and let the single-channel experience dictate the schema — not the other way around.

The deepest principle, stated once more because everything depends on it: **the system must be able to regenerate its own beliefs from immutable evidence on demand. Any part of it that cannot be regenerated can rot, and rotting is guaranteed.**

## 23. QUESTIONS WE HAVE NOT ASKED BUT SHOULD

1. **What is the actual decision throughput of The Institution?** If it's ~5 consequential decisions per month, the entire system can be far smaller than this design implies. Right-size to throughput, not corpus size.
2. **What exactly did the single video answer that planning missed?** That delta is your ground-truth extraction specification. If you can't enumerate it, you don't yet know what to extract from the other 9,999 videos.
3. **How will we measure briefing precision?** Without an eval harness, you cannot distinguish "more useful as it grows" from "bigger as it grows."
4. **Who owns the schema and the procedures?** This is the most powerful and most dangerous role in the system.
5. **What is the recall plan when a source is found wrong en masse** (retraction, debunk, creator pivot)? Retraction propagation must be designed before it's needed.
6. **What is our update-latency requirement?** "Current" for a fast-moving repo might mean 72 hours; that requirement shapes the pipeline far more than source count does.
7. **What are we NOT monitoring?** The corpus should maintain an explicit gaps register — domains with no claims, creators we've never ingested, questions with no assessments. An intelligence system that can't represent its own ignorance will systematically overstate its coverage.
8. **Can we re-derive a standing assessment if we distrust the extractor that produced it?** If the answer is no, provenance is decorative.
9. **What are the legal/terms-of-service constraints on transcript mining and storage**, and what's our policy for creator attribution?
10. **Is the corpus a crown jewel, and is it treated like one?** Backups, portability, exportability, and independence from any single model provider. A system this valuable that dies with one vendor contract is not an institution — it's a dependency.
11. **Correlated failure:** all extraction models share training-data biases. What is our plan for the class of errors they all make *together*? (Partial answer: diverse-model verification, human audits, and eventually our own experiments as ground truth — but the residual risk should be named.)
12. **How does a new subsystem enter the corpus?** Schema evolution and onboarding procedure for new domains is where most such systems quietly die; design it while the schema is still small.

---

One closing note from the Council: the proposal's instincts are unusually good. Its dangers are not stupidity but ambition — the desire to model the domain elegantly, to resolve contradictions cleanly, to let the system improve itself. Each of those, unmanaged, converts the system from an intelligence asset into a highly organised generator of confident error. The architecture above is designed so that even when components fail — and they will — the failure is visible, traceable, and reversible. That, in the end, is the only honest definition of reliable.






Qwen:

### 1. EXECUTIVE VERDICT

The Institution is attempting to build a passive repository in an active, adversarial, and rapidly decaying information environment. The current proposal describes a highly sophisticated, well-intentioned wiki, not an intelligence system. 

The core conceptual flaw is treating "discoveries" as static entities rather than **context-dependent hypotheses**. In AI infrastructure, a claim like "prefix caching saves memory" is not a fact; it is a probabilistic assertion that is only true under specific hardware, model, and workload constraints. 

To survive and scale, The Institution must abandon the concept of a "Knowledge Base" and instead build an **Active Epistemic Engine**—a system that manages uncertainty, models environmental context, and autonomously drives empirical validation.

### 2. WHAT WE ARE ACTUALLY BUILDING

We are not building a knowledge graph, a database, or a retrieval system. We are building a **Context-Bounded Assertion Graph** coupled with an **Automated Experimentation Harness**. 

It is an engine that converts external noise into localized, empirically verified engineering constraints. It does not just store what others have said; it models the *performance surface* of the domain and continuously tests the boundaries of those models against the physical reality of The Institution’s hardware.

### 3. WHAT IS RIGHT ABOUT THE CURRENT PROPOSAL

*   **CQRS Principle:** "Store once as structured data; render many different views" is architecturally sound and necessary.
*   **Domain Modeling:** "Summaries describe sources; the system should model the domain" is the correct philosophical pivot.
*   **Pipeline Directionality:** The 7-layer pipeline (Raw $\to$ Extract $\to$ Knowledge $\to$ Validate $\to$ Decide) conceptually separates raw data from compiled decisions.
*   **Target Identification:** Recognizing that "boring" architectural primitives (Proxmox, LXC, parser configs) are often more critical than flashy model announcements.

### 4. WHAT IS WRONG / MISSING / DANGEROUS

*   **The "Video" Fallacy:** The proposal states: *"The video is the source."* This is dangerously wrong. The video is a **lossy, biased broadcast** of the source. The actual sources are the code, the hardware, and the physics of the compute. Relying on the video as the primary source introduces the creator's blind spots and presentation biases.
*   **Context Blindness:** Extracting "discoveries" without strict environmental context creates liabilities. A discovery without context is misinformation waiting to happen.
*   **Passive Validation:** Waiting for humans to notice gaps and design experiments creates a fatal bottleneck.
*   **Missing Dependency Graph:** Architecture is a web of dependencies. If `llama.cpp` updates its KV-cache management, every downstream assertion relying on the old behavior must be automatically flagged.
*   **AI Circular Poisoning:** If synthesis agents write to the knowledge base, and extraction agents use the knowledge base to guide future extraction, you create an echo chamber of hallucinated consensus.

### 5. RECOMMENDED INFORMATION MODEL

Shift from "Knowledge Objects" to **Context-Bounded Engineering Assertions (CBEAs)**.

An assertion is not a fact; it is a probabilistic claim bounded by environment parameters.
**Structure:** `Assertion { Claim, Required_Context, Predicted_Outcome, Confidence, Evidence_Pointers }`

*   *Bad:* "Chunked prefill improves throughput."
*   *Good:* "Under Context (GPU: 24GB VRAM, Model: 70B, SeqLen > 8k), Chunked Prefill yields a 15% throughput increase over Standard Prefill, with a 5% TTFT penalty."

### 6. RECOMMENDED DATA / KNOWLEDGE STRUCTURE

The system requires a multi-stratum architecture:
1.  **Raw Stratum:** Immutable transcripts, code diffs, binaries, benchmark logs.
2.  **Evidence Stratum:** Atomic extractions (metrics, configs, versions) linked directly to raw spans.
3.  **Ontology Stratum:** The schema of components, hardware, and dependencies (The "Map").
4.  **Assertion Stratum:** The CBEAs (The "Claims").
5.  **State Stratum:** The Institution's current hardware, software, and strategic constraints (The "Reality Anchor").
6.  **Decision Stratum:** Active configurations chosen by The Institution.

### 7. HOW RAW INFORMATION BECOMES INTELLIGENCE

Raw text is processed to extract **parameters and constraints**, not summaries. 
The LLM acts as a compiler, translating unstructured human speech into structured environmental constraints. Intelligence is generated when the system intersects a new Assertion with the Institution's *State Stratum* to calculate **Relevance** and **Impact**. If an assertion requires 80GB VRAM and The Institution has 24GB cards, the assertion is immediately marked as `Low Relevance / Future Watch`, preventing noise accumulation.

### 8. HOW CROSS-SOURCE SYNTHESIS SHOULD WORK

Do not synthesize by "merging summaries." Synthesize by **intersecting contexts**.

If 30 videos discuss prefix caching, the system maps them onto a multidimensional grid (X: Context Length, Y: VRAM, Z: Latency). Synthesis is the generation of a **Performance Response Surface**. 
Contradictions are not resolved by voting or averaging; they are resolved by identifying the hidden variables. (e.g., *Video A claims prefix caching is fast. Video B claims it is slow. The system detects Video A used FlashAttention-2 and Video B did not. The contradiction is resolved into a new dependency rule.*)

### 9. HOW TEMPORAL CHANGE SHOULD WORK

Knowledge does not simply "decay." It is **invalidated by environmental shifts**.
Implement a **Version-Aware Dependency Graph**. Every assertion must declare the software versions it relies on. When a new release of `llama.cpp` drops, the system traverses the graph and automatically downgrades the confidence of all dependent assertions to `REQUIRES_REVALIDATION`. Old knowledge isn't deleted; it is archived with a tombstone explaining *why* it was superseded.

### 10. HOW TRUST / EVIDENCE SHOULD WORK

Trust is not a scalar score for a creator. It is a dynamic vector calculated per assertion based on:
*   **Proximity:** Did they show the code/benchmark, or just talk about it?
*   **Reproducibility:** Are the exact configs and commit hashes provided?
*   **Hardware Relevance:** Is their test environment similar to ours?
*   **Corroboration:** Is this supported by independent sources or official code?
Trust is calculated dynamically. A highly trusted creator making a claim outside their proven domain (e.g., a modeling expert talking about networking) receives a lower trust score for that specific assertion.

### 11. HOW INSTITUTION VALIDATION SHOULD WORK

External knowledge is strictly classified as **Hypothesis**. It only becomes **Institution Validated Knowledge** when tested locally.
The system maintains a prioritized queue of Hypotheses ranked by: `(Architectural Impact * Uncertainty) / Test Cost`.
Automated agents provision isolated environments (LXC/Proxmox), execute the exact configurations, measure the outcomes, and update the Assertion confidence to 100% (or 0% if it fails). Validated knowledge is cryptographically or strictly separated from External knowledge.

### 12. HOW DECISIONS SHOULD BE REPRESENTED

Decisions are **Active Configurations** bound to Validated Assertions.
Every decision includes a **Fallback Chain** (e.g., *"Decision: Use Chunked Prefill. Basis: Assertion #452 (validated on our hardware). Contingency: If VRAM < 24GB, fallback to Assertion #455"*).
Decisions are compiled views of the highest-confidence assertions that satisfy current constraints. A future AI can trace any runtime behavior back to the exact decision, assertion, and raw evidence that justified it.

### 13. HOW ACTIONABILITY SHOULD WORK

Every assertion must compute an **Actionability Score** based on:
*   **Relevance:** Does this solve a current Institution bottleneck?
*   **Cost of Ignorance:** What breaks or what opportunity is lost if we don't adopt this?
*   **Test Cost:** How many compute hours to validate this locally?
*   **Dependencies:** What else must be built/changed before we can use this?

### 14. HOW THE SYSTEM SHOULD SCALE

*   **10 Sources:** Manual review of extractions.
*   **100 Sources:** AI clustering of assertions, human review of high-impact contradictions.
*   **10,000 Sources:** Automated experiment generation for high-uncertainty, high-impact assertions. RAG is replaced by Graph Traversal + Constraint Solving.
Scale is achieved by pushing processing down the stack: don't store more text, store more *parameters of the performance surface*.

### 15. HOW MULTIPLE AI WORKERS SHOULD INTERACT WITH IT

Strict separation of duties using an **Append-Only Evidence Ledger**.
*   **Harvester Agents:** Read-only on sources, write to Evidence Layer.
*   **Synthesizer Agents:** Read Evidence, write to Assertion Layer (but *cannot overwrite* existing assertions, only add new context-bounded ones).
*   **Critic Agents:** Read Assertions, flag contradictions, generate Hypothesis/Experiment tickets.
*   **Executor Agents:** Run experiments, write to Validation Layer.
No agent can edit the raw source or directly alter a Decision without a validated experiment ticket. This prevents circular poisoning.

### 16. WHETHER / WHEN TO USE RAG

**Avoid RAG over the Knowledge/Assertion Layer.** 
Use structured graph queries (Cypher, GraphQL, SQL) to reason over the Assertion Graph. You cannot do complex engineering reasoning via vector similarity.
Use RAG *only* as a drill-down tool over the **Raw Stratum** when a human or agent needs to read the exact transcript context of an atomic fact. Vector search is for *discovery*; Graph traversal is for *engineering*.

### 17. WHAT SHOULD BE AUTOMATED

*   Ingestion, transcription, and raw formatting.
*   Atomic parameter extraction (configs, metrics, versions).
*   Context clustering and contradiction flagging.
*   **Test harness generation** (writing the scripts/containers to validate a claim locally).
*   Dependency graph traversal and invalidation on version bumps.

### 18. WHAT SHOULD REQUIRE HUMAN JUDGEMENT

*   Defining the Institution's current hardware and strategic constraints (The State Stratum).
*   Resolving deep architectural trade-offs (e.g., "Do we prioritize latency or throughput for this specific workload?").
*   Reviewing the *design* and cost of generated experiments before they consume expensive compute.
*   Final approval of architectural shifts (The Decision Layer).

### 19. MINIMUM VIABLE VERSION

*   A local Graph DB (e.g., Neo4j, FalkorDB) + Vector DB.
*   Ingestion pipeline for 10 YouTube transcripts.
*   LLM prompt engineered to extract `Component`, `Configuration`, `Metric`, `Condition`.
*   A simple dashboard showing clusters of claims around key primitives (llama.cpp, prefix caching).
*   Manual tracking of "We tested this, it worked/failed" in a spreadsheet or simple DB table.

### 20. LONG-TERM VERSION

*   Fully autonomous Epistemic Engine.
*   Agents continuously monitor GitHub, Arxiv, YouTube, and internal telemetry.
*   System automatically spins up LXC containers to test regressions when `llama.cpp` or `vLLM` pushes a new commit.
*   The "Intelligence Briefing" is dynamically generated based on the delta between current system state and the optimal state defined by the Assertion layer.

### 21. FAILURE MODES WE MUST DESIGN AGAINST

*   **Context Collapse:** Merging a 70B model claim with an 8B model claim, resulting in a "frankenstein" recommendation that fails on both.
*   **Zombie Knowledge:** Old claims persisting and being recommended after a library update changes the underlying behavior.
*   **Echo Chamber:** Agents validating agents without physical execution, creating a false consensus.
*   **Paralysis by Analysis:** Thousands of low-impact hypotheses clogging the experiment queue, starving high-impact architectural questions of compute.

### 22. CONCRETE RECOMMENDATION

**Do not build a "Knowledge Base." Build an Epistemic Engine.** 

Shift the focus from *"What did the video say?"* to *"What are the exact parameters of the environment in which this claim holds true?"* Treat all external information as unverified hypotheses until proven on Institution hardware. Build the dependency graph first, and the extraction pipeline second. The value of this system will not be in how much it remembers, but in how accurately it tells The Institution what it *does not yet know* and how to find out.

### 23. QUESTIONS WE HAVE NOT ASKED BUT SHOULD

*   **How do we handle negative results?** YouTube videos rarely show what failed, but our local experiments will. Negative results are highly valuable and must be first-class citizens in the Assertion Graph.
*   **How do we model cost?** A technique might be 10% faster but cost 50% more in cloud compute or VRAM. The ontology must include resource economics.
*   **How do we detect implicit assumptions?** (e.g., The creator assumes a 10Gbps network between nodes, or NVLink, which The Institution does not have).
*   **How do we measure the ROI of the intelligence system itself?** We must track how many compute hours, engineering hours, or dead-ends this system prevented, to justify its own compute and API costs.









Deepseek:


1. EXECUTIVE VERDICT

The current proposal is directionally better than a RAG dump or a summaries archive, but it is not yet an architecture. It is a set of plausible layers that, as specified, would most likely degrade into a well-tagged collection of notes.

The central conceptual mistake is treating “source” or “discovery” as the primary unit of intelligence. It should be the **claim**, grounded in **evidence**, interpreted against a **canonical topic**, and only later promoted to **institution finding** or **decision**.

You do not need a knowledge graph. You need an append-only, claim-evidence-position-decision system with a generated graph view for provenance and conflict. The graph is a query pattern, not the storage foundation.

The most important requirement is not “more information.” It is **compression without loss of critical engineering meaning**: many videos should collapse into few, current, conditional, sourced positions that can directly drive decisions.

---

2. WHAT WE ARE ACTUALLY BUILDING

This is an **institutional engineering intelligence system**.

More precisely, it is:

- a machine-readable, continuously updated model of engineering claims relevant to The Institution;
- an evidence trail linking claims to sources, experiments, and decisions;
- a validation boundary separating “something someone said” from “something we believe” and “something we tested”;
- a decision-support system, not a general knowledge base;
- a curated intelligence layer over heterogeneous source material.

It is not primarily a document store, a wiki, a RAG system, or an ontology. Those may appear as components or views.

The core loop is:

```
Raw source → Evidence → Claim → Topic/Position → Hypothesis → Test → Finding → Decision
```

Everything else is supporting infrastructure.

---

3. WHAT IS RIGHT ABOUT THE CURRENT PROPOSAL

Several instincts are correct:

- **Immutable raw archive** — essential for provenance and re-extraction.
- **Separation of raw, evidence, knowledge, validation, decision** — correct in principle.
- **“Store once as structured data; render many views”** — correct.
- **External knowledge must not automatically become Institution truth** — critical.
- **Decision provenance** — correct and often missing from such systems.
- **Attention to boring architectural primitives** — correct; LXC, parser config, state management, and restartability matter more than model announcements.
- **Avoiding a Markdown book or summary pile** — correct.
- **The eventual need for synthesis, not just retrieval** — correct.

But these instincts have not yet been turned into an operational data model.

---

4. WHAT IS WRONG / MISSING / DANGEROUS

The proposal has several hidden failure modes.

### 4.1 “The video is the source; the discovery is the unit” is wrong

A video is a container. A “discovery” is too vague. The useful unit is a **claim**:

- “prefix caching improves throughput by X under conditions Y”
- “tool-call parser Z fails under long agentic workloads”
- “quantisation Q degrades instruction following after N turns”

A source can contain many claims. One claim can be supported by many sources.

If “discovery” becomes the unit, you will create objects like “video discusses prefix caching,” which is a summary, not intelligence.

### 4.2 No lifecycle or state machine

What happens to a claim when new evidence contradicts it?

It must not be edited silently. It must move through states such as:

```
extracted
deduplicated
assessed
corroborated
contested
superseded
rejected
institution_hypothesis
institution_validated
```

Without this, the corpus becomes a growing pile of conflicting assertions with no resolution.

### 4.3 Extraction may still become summarisation

The proposal says “extract structured observations,” but if extraction produces prose summaries, it will lose exact versions, commands, conditions, and failure details.

Extraction must preserve:

- exact verbatim evidence spans;
- configuration blocks;
- version strings;
- command-line flags;
- hardware conditions;
- workloads;
- observed metrics.

Summaries describe sources. Evidence preserves meaning.

### 4.4 No canonical identity/deduplication

Thirty videos mention “prefix caching.” Some mean:

- the same technique;
- different implementations;
- different model runtimes;
- different hardware;
- different contexts.

Without canonical topics and aliases, the system will create thirty disconnected notes. This is one of the largest scale risks.

### 4.5 No conflict model

Contradictions are normal. The system must explicitly represent them:

- Claim A: “MTP improves llama.cpp performance.”
- Claim B: “MTP hurts performance on constrained GPUs.”

These may both be true under different conditions. Or one may be outdated. Or one may be wrong.

A system that merely stores both as separate knowledge objects has not done its job.

### 4.6 Trust is under-specified

“Source trust” cannot be a single score. A creator may be authoritative on llama.cpp but not on cloud networking. A video demo may be weak evidence even if the creator is usually reliable.

Trust must be computed per claim, from evidence type, method, conditions, independence, and recency.

### 4.7 No write model for multiple AI workers

If multiple Hermes instances or coding agents can write claims, hypotheses, and findings, the system can be corrupted by:

- duplicate claims;
- hallucinated evidence;
- circular reinforcement;
- contradictory edits;
- lost provenance.

The proposal mentions workers but does not define a safe write path.

### 4.8 RAG is implicitly assumed but not examined

RAG over raw transcripts or summaries is not the same as intelligence. It retrieves passages; it does not synthesise.

If RAG becomes the primary interface, you will get plausible answers that may be stale, contextless, or contradicted by later evidence.

### 4.9 Temporal knowledge is not modelled deeply enough

AI infrastructure changes quickly. A recommendation may be valid for:

- a specific llama.cpp commit;
- a specific model generation;
- a specific GPU;
- a specific driver;
- a specific quantisation;
- a specific use case.

Without versioned context, old advice becomes dangerous.

### 4.10 The system may become self-important

There is a risk that the intelligence system becomes the main project rather than a tool supporting The Institution’s actual engineering. Every layer, view, and automation must earn its cost.

---

5. RECOMMENDED INFORMATION MODEL

The system should be built around the following core object types.

### 5.1 Source

A raw source: video, transcript, article, GitHub repo, release note, benchmark, or document.

Fields:

- `source_id`
- `source_type`
- `url`
- `channel/author`
- `published_at`
- `retrieved_at`
- `content_hash`
- `raw_text_location`
- `metadata`

### 5.2 Evidence

A specific span inside a source that supports or contradicts a claim.

Fields:

- `evidence_id`
- `source_id`
- `start_offset`
- `end_offset`
- `quote_text`
- `evidence_type`
- `content_hash`
- `extraction_model`
- `extracted_at`

Evidence is immutable. If extraction improves, new evidence is added; old evidence is not silently changed.

### 5.3 Claim

A falsifiable statement extracted or inferred from evidence.

Example:

```
subject: prefix caching
predicate: improves tokens/s
value: +35%
conditions: llama.cpp, Q4_K_M, 3090, long context
valid_at: 2026-09
evidence: E123
```

A claim should be atomic and typed. Do not store prose summaries as claims.

### 5.4 Topic / Canonical Entity

The stable subject of claims: `prefix_caching`, `mtp_speculative_decoding`, `hermes_tool_parser`, `lxc_worker_isolation`.

Fields:

- `topic_id`
- `canonical_name`
- `aliases`
- `description`
- `category`
- `domain`
- `volatility`

Canonical topics should remain bounded. Most new source material should link to existing topics, not create new ones.

### 5.5 Position

The current synthesised stance on a topic, generated from all active claims.

Example:

```
Topic: prefix_caching
Position: beneficial for long-context local inference on 3090-class GPUs;
          unproven on 5060 Ti; not yet adopted.
Status: contested
Confidence: moderate
Valid_as_of: 2026-09-08
```

Positions are generated and versioned, not hand-edited.

### 5.6 Hypothesis

A testable Institution-specific statement derived from external claims.

Example:

```
Hypothesis: prefix caching improves tokens/s by >10% on our local LXC worker
under 8K context with Hermes 4 + llama.cpp commit abc123.
```

### 5.7 Experiment

A test of a hypothesis.

Fields:

- `experiment_id`
- `hypothesis_id`
- `environment`
- `script/artifacts`
- `metrics`
- `result`
- `raw_output_location`
- `status`

Negative results are first-class.

### 5.8 Finding

An Institution-validated result.

Example:

```
Finding: prefix caching improved tokens/s by 14% on our 5060 Ti under
read-heavy agentic workload, but increased memory by 9%.
```

### 5.9 Decision

A commitment made by The Institution.

Fields should include:

- `decision_id`
- `statement`
- `status`
- `rationale`
- `linked findings`
- `linked claims`
- `linked experiments`
- `scope`
- `superseded_by`
- `review_date`

A decision is not the same as truth. It is a choice made under conditions.

### 5.10 Relationship

Edges between objects:

- `supersedes`
- `conflicts_with`
- `corroborates`
- `depends_on`
- `same_as`
- `version_of`
- `implements`
- `invalidates`

This gives you graph-like provenance without requiring a graph database.

---

6. RECOMMENDED DATA / KNOWLEDGE STRUCTURE

Use a relational database as the primary system of record. PostgreSQL is the pragmatic default.

Core tables:

```text
sources
evidence
claims
claim_evidence
topics
aliases
claim_topics
relationships
hypotheses
experiments
findings
decisions
decision_links
positions
position_history
audit_log
```

Use JSONB for variable context:

```json
{
  "hardware": "RTX 5060 Ti 16GB",
  "software": "llama.cpp b4567",
  "model": "Hermes 4 Q4_K_M",
  "workload": "long agentic tool calls",
  "context_len": 8192,
  "batch_size": 4
}
```

Store raw transcripts in object storage or compressed files, not in the main database.

Every table gets:

- immutable UUID primary key;
- `created_at`;
- `created_by`;
- `content_hash`.

Current positions are regenerated from claims. History is append-only.

Do not build a full ontology initially. A controlled taxonomy for engineering topics is enough.

---

7. HOW RAW INFORMATION BECOMES INTELLIGENCE

The pipeline should be:

### Stage 1 — Ingest

- Download transcript/source text.
- Store raw text and metadata immutably.
- Compute content hash.

### Stage 2 — Extract evidence spans

- Split transcript into segments.
- Extract exact quotes, config blocks, commands, version strings.
- Do not summarise.

### Stage 3 — Model claims

From evidence, create typed claims:

- technique claim;
- performance claim;
- configuration claim;
- failure claim;
- compatibility claim;
- architecture claim.

Each claim must reference at least one evidence span.

### Stage 4 — Link to topics

Resolve aliases and synonyms.

Examples:

- “MTP” → `mtp_speculative_decoding`
- “spec decode” → `speculative_decoding`
- “KV cache quant” → `kv_cache_quantisation`

### Stage 5 — Deduplicate

If an identical claim already exists, add the new evidence as corroboration. Do not create a duplicate claim.

### Stage 6 — Detect conflict

If a new claim contradicts an existing claim, create a conflict relationship. Do not silently choose a winner.

### Stage 7 — Synthesise positions

Regenerate the current position for the affected topic.

### Stage 8 — Generate hypotheses

For topics relevant to Institution hardware or architecture, generate testable hypotheses.

### Stage 9 — Queue experiments

Based on priority, effort, and expected impact.

### Stage 10 — Validate and decide

Experiments produce findings. Findings inform decisions.

### Stage 11 — Compile views

Generate briefs from current positions, findings, and decisions.

---

8. HOW CROSS-SOURCE SYNTHESIS SHOULD WORK

For each canonical topic, the system should group all active claims.

Then it should ask:

- Are these the same technique?
- Are they different implementations?
- Are conditions different?
- Do they agree or conflict?
- Which evidence is strongest?
- Which claims are outdated?
- What is the current Institution-relevant meaning?

A synthesis algorithm should:

1. Normalise aliases to the canonical topic.
2. Cluster claims by predicate and value type.
3. Check for contradictions.
4. Compare evidence quality:
   - source code inspection;
   - official documentation;
   - reproducible benchmark;
   - demo;
   - opinion.
5. Check temporal/version context.
6. Check independence. Do not count five videos repeating the same claim as five independent confirmations if they all derive from one original source.
7. Produce a position with:
   - current best understanding;
   - supporting evidence count;
   - contradicting evidence count;
   - conditions under which it holds;
   - confidence;
   - last updated;
   - recommended action.

Thirty videos about prefix caching should produce **one current position**, not thirty recommendations.

---

9. HOW TEMPORAL CHANGE SHOULD WORK

Temporal validity must be explicit.

Each claim should carry:

- `observed_at`;
- `valid_from`;
- `valid_until`;
- `applies_to_versions`;
- `source_published_at`;
- `retrieved_at`.

Positions should carry:

- `valid_as_of`;
- `review_due`;
- `domain_volatility`.

Rules:

- Age alone does not invalidate a claim.
- New evidence can mark an old claim as `superseded`.
- A new release note or source-code change can invalidate a claim.
- A benchmark on newer hardware can supersede an older benchmark.
- If a claim is uncontested but old, it remains active but should be reviewed periodically based on domain volatility.
- Superseded claims are never deleted. They are excluded from active briefs but remain in history.

Example:

```text
Claim: llama.cpp MTP improves tokens/s by 20% on 3090.
Status: superseded by commit abc123 result showing +8% on modern build.
```

Briefs should only show current positions, with links to what superseded them.

---

10. HOW TRUST / EVIDENCE SHOULD WORK

Trust is not a global creator score. It is a function of the claim’s evidence.

Assess each claim along these dimensions:

- **Evidence class**: source code, official docs, reproducible benchmark, demo, anecdote.
- **Method**: direct inspection, measured test, inference, opinion.
- **Specificity**: exact hardware/software/version, or vague.
- **Recency**: relative to current software versions.
- **Relevance**: does the context match Institution hardware/workload?
- **Reproducibility**: can we rerun the test?
- **Independence**: are multiple sources genuinely independent?
- **Technical authority**: is the source authoritative in this specific domain?
- **Conflict**: is the claim contested?
- **Corroboration**: how many independent evidence sources support it?

Confidence levels might be:

```text
unverified
weak
moderate
strong
institution_validated
```

A demo by an excellent creator is still not equivalent to our own benchmark. A source can be authoritative in one area and wrong in another.

---

11. HOW INSTITUTION VALIDATION SHOULD WORK

Create a hard boundary.

### Class A — External Claim

Something claimed outside The Institution.

```text
Status: external
Meaning: possibly useful, not yet adopted
```

### Class B — Institution Hypothesis

A testable statement derived from external claims but expressed against Institution hardware/software/workload.

```text
Status: hypothesis
Requires: experiment design
```

### Class C — Institution Finding

A result from an experiment on Institution hardware or direct source inspection.

```text
Status: validated
Scope: exact hardware, software, workload, date
```

### Class D — Institution Decision

A commitment.

```text
Status: adopted/rejected/deferred
```

Promotion from external claim to institution truth must require:

- a relevant experiment; or
- direct source-code inspection; or
- official documentation exactly matching our version and use case.

Popularity, creator reputation, or repeated mention is not enough.

Every hypothesis should include:

```text
if true, expected observable result
if false, expected observable result
environment
metrics
pass/fail threshold
```

Negative results must be stored. They prevent repeating bad ideas.

---

12. HOW DECISIONS SHOULD BE REPRESENTED

Decisions should be structured like lightweight Architecture Decision Records.

Fields:

```text
decision_id
title
status: proposed / accepted / rejected / superseded / deferred
statement
rationale
constraints
alternatives considered
linked findings
linked claims
linked experiments
scope
owner
created_at
review_date
superseded_by
```

A decision can be traced:

```text
Decision
  → Finding
    → Experiment
      → Hypothesis
        → Claim
          → Evidence
            → Source
```

This chain must be queryable, not just documented.

If an underlying finding changes, linked decisions should be flagged for review.

---

13. HOW ACTIONABILITY SHOULD WORK

Not every claim needs action. But every **position** or **finding** that matters should answer:

- What is it?
- Why does it matter?
- What does it affect?
- What evidence supports it?
- How confident are we?
- What conditions does it depend on?
- What could invalidate it?
- What should The Institution do?
- What should we test?
- What happens if we ignore it?

Additional fields:

```text
urgency
effort
cost
risk
reversibility
dependencies
owner
monitoring_impact
blast_radius
```

Architectural primitives should be prioritised using:

- **Leverage**: affects many subsystems.
- **Irreversibility**: hard to change later.
- **Blast radius**: failure affects everything.
- **Cost of late adoption**: migration cost if ignored.

Boring topics like LXC isolation or parser configuration often score highly here.

---

14. HOW THE SYSTEM SHOULD SCALE

At 10 sources:

- manual curation is fine;
- focus on schema and extraction quality;
- validate one complete decision chain.

At 100 sources:

- automate extraction and topic linking;
- human review only high-priority conflicts.

At 1,000 sources:

- deduplication and synthesis must be automated;
- canonical topics must remain stable;
- brief generation must be automated.

At 10,000+ sources:

- only exceptions need human review;
- raw archive becomes enormous but evidence spans remain queryable;
- source prioritisation is required; not all sources are equal.

What should be:

### Automated

- ingestion
- transcript alignment
- evidence extraction
- claim modelling
- deduplication
- topic linking
- conflict detection
- position regeneration
- brief generation
- stale flagging
- source discovery

### AI-assisted

- synthesis drafting
- hypothesis generation
- experiment design
- gap detection
- priority scoring

### Human-reviewed

- final decisions
- high-impact contradictions
- risky experiments
- canonical topic creation
- validation on physical hardware
- security-sensitive changes

### Immutable

- raw sources
- evidence spans
- experiment raw results
- decision log history
- audit log

### Regenerated

- all compiled views
- all positions
- embeddings
- search indices

---

15. HOW MULTIPLE AI WORKERS SHOULD INTERACT WITH IT

Assume every AI worker is potentially wrong.

Rules:

- All writes are append-only proposals.
- No worker edits a canonical position directly.
- Every claim must cite evidence spans.
- A claim generated by another AI is not evidence.
- Duplicate claims are suppressed by content hash and normalised form.
- Contradictions create conflict records, not silent overwrites.
- Workers have roles and permissions:
  - extractor;
  - synthesiser;
  - experiment designer;
  - reviewer.
- A synthesiser cannot promote its own draft to an institution finding.
- Independent corroboration requires different evidence sources, not just different AI workers.
- The audit log records which worker created what, with which model and prompt version.

Circular reinforcement is prevented because AI-generated positions cannot be used as evidence for new claims.

---

16. WHETHER / WHEN TO USE RAG

Do **not** start with RAG.

Initially, use structured querying over claims, topics, positions, findings, and decisions.

RAG should be introduced later only for:

- retrieving exact evidence spans from raw transcripts;
- exploratory search over unstructured source material;
- answering questions where exact structured retrieval is insufficient.

Even then:

- RAG should search raw evidence, not summaries.
- Answers must cite exact evidence spans.
- RAG should not be the primary decision engine.
- If a question can be answered from structured positions, do that first.

RAG is retrieval. It is not synthesis, validation, or reasoning.

A better long-term approach is hybrid:

```text
structured query for known topics/positions
+ vector search over raw evidence for supporting passages
+ SQL join for provenance
```

---

17. WHAT SHOULD BE AUTOMATED

Automate everything that is mechanical or high-volume:

- source discovery and download;
- transcript extraction and alignment;
- evidence span extraction;
- claim extraction with exact quotes;
- content hashing;
- deduplication;
- alias resolution;
- topic linking;
- conflict detection;
- version tracking;
- position regeneration;
- report/brief generation;
- stale flagging;
- source priority scoring;
- experiment queuing for safe, scripted tests.

Automation should always produce provenance and be reproducible.

---

18. WHAT SHOULD REQUIRE HUMAN JUDGEMENT

Human judgement should be reserved for:

- final architecture decisions;
- irreversible changes;
- security-sensitive changes;
- physical hardware experiments;
- resource allocation;
- high-impact conflicting evidence;
- deciding whether an external source is authoritative in a new domain;
- creating new canonical topics when clustering is ambiguous;
- approving self-improvement changes to the intelligence system itself;
- deciding when a topic is settled enough to reduce collection effort;
- final review of compiled intelligence used by planning agents.

The system should present humans with decisions, not documents.

---

19. MINIMUM VIABLE VERSION

Build this first:

- PostgreSQL or SQLite.
- Tables: sources, evidence, claims, topics, claim_topics, relationships, hypotheses, experiments, findings, decisions.
- A simple extraction pipeline for YouTube transcripts.
- Manual or assisted extraction of evidence spans.
- Manual topic curation for first 50 topics.
- One generated view: topic position report.
- One generated view: contradiction report.
- One decision chain example end-to-end.

No RAG.
No graph database.
No multi-agent writes.
No heavy ontology.

Goal: prove that 10 videos become 5 validated findings and 1 institution decision, with full provenance.

If that does not work, no amount of scale will fix it.

---

20. LONG-TERM VERSION

The long-term system should become:

- append-only evidence store;
- continuously extracted claim stream;
- automatically synthesised topic positions;
- institution validation pipeline;
- decision provenance system;
- self-monitoring gap detector;
- safe experiment runner;
- compiled intelligence service for planning agents.

The self-improvement loop is realistic, but should be staged:

1. Human-designed extraction and validation.
2. AI-assisted extraction and synthesis.
3. AI-proposed experiments.
4. AI-run safe experiments in sandbox.
5. AI-proposed decisions, human-approved.
6. AI-recommended source discovery and gap research.

Full autonomous loop should only exist where failure is cheap and reversible.

---

21. FAILURE MODES WE MUST DESIGN AGAINST

1. **Summary compression** losing exact commands, versions, and conditions.  
   → Store evidence spans, not summaries.

2. **False consensus** from many sources repeating one original claim.  
   → Track independence, not just count.

3. **Recency bias** where new but weaker evidence overrides older stronger evidence.  
   → Compare evidence class and conditions, not just date.

4. **Circular reinforcement** by AI workers citing each other.  
   → AI outputs cannot be evidence.

5. **Ontology overgrowth** creating thousands of vague entities.  
   → Controlled canonical topics and aliases.

6. **Silent mutation** of conclusions losing history.  
   → Append-only claims, regenerated positions, full audit.

7. **Hallucinated extraction** without source verification.  
   → Require exact quote spans and hashes.

8. **Scale without synthesis** producing 100,000 disconnected facts.  
   → Topic-based position synthesis.

9. **Multi-agent write conflicts** corrupting claims.  
   → Append-only proposal model with schema validation.

10. **External claims becoming institution truth** without validation.  
    → Hard validation boundary.

11. **Stale advice remaining active** after software changes.  
    → Versioned validity and supersession edges.

12. **RAG over summaries** producing plausible but false answers.  
    → Structured query first; RAG only over evidence.

13. **Decision trace broken** because links are missing.  
    → Enforce linked claims/evidence/experiments in decisions.

14. **Intelligence system becoming the project** rather than supporting engineering.  
    → Ruthlessly prioritise value per hour.

15. **Overfitting to YouTube** while ignoring source code and official docs.  
    → Evidence class weighting.

16. **Absence of negative results** causing repeated failed ideas.  
    → Store negative findings as first-class.

---

22. CONCRETE RECOMMENDATION

Build the following:

1. An append-only **relational evidence store**, not a knowledge graph.
2. Use **claim** as the atomic unit of intelligence.
3. Use **evidence spans** as the grounding layer.
4. Use **canonical topics** for synthesis and deduplication.
5. Regenerate **positions** from active claims.
6. Keep **external claim**, **institution hypothesis**, **institution finding**, and **decision** strictly separate.
7. Add **temporal validity** to every claim and position.
8. Add **supersedes** and **conflicts** edges.
9. Build a **decision log** with full provenance chain.
10. Avoid RAG initially. Add it later over raw evidence only.
11. Automate extraction, deduplication, conflict detection, and brief generation.
12. Reserve human judgement for decisions, high-impact conflicts, and validation.
13. Start with 10 sources and prove one complete chain before scaling.

The right mental model is:

```text
Institutional memory with an audit trail,
not a library with better tags.
```

---

23. QUESTIONS WE HAVE NOT ASKED BUT SHOULD

1. How will planning/coding agents actually query this system? What is the token budget for a compiled brief?
2. How will we measure whether the intelligence system improves engineering outcomes?
3. How do we handle source licensing, transcript copyright, and creator deletions?
4. How do we handle non-English or low-quality transcripts?
5. How do we version extraction prompts and models so extraction quality changes are traceable?
6. How do we integrate our own operational telemetry as first-class evidence?
7. How do we represent unknown unknowns and negative results?
8. What is the maintenance cost of the intelligence system relative to actual engineering work?
9. How do we handle creator corrections, retractions, or conflicts of interest?
10. How do we calibrate confidence so “moderate” means the same thing across topics?
11. How do we avoid optimising for interesting content over important boring content?
12. How do we decide when a topic is settled and no longer requires active collection?
13. How do we prevent the system from becoming an expensive alternative to reading changelogs?
14. How do we handle evidence that is only partially relevant to our hardware?
15. How do we safely introduce self-improvement without circular validation?

The greatest risk is not lack of information. It is building a system that confuses volume with intelligence, and documentation with decision-making.









Mistral:


# **THE INSTITUTION — COUNCIL REVIEW: INTELLIGENCE SYSTEM ARCHITECTURE**
*Independent Critique & Recommendation*
*Council of Wise Ones — Mike D., 2026-09-08*

---

---

## **1. EXECUTIVE VERDICT**

**The current proposal is directionally correct but structurally insufficient for the scale, velocity, and reliability requirements of The Institution.**

It risks **information collapse** (30 videos on prefix caching → 30 disconnected facts), **temporal poisoning** (2026 truths becoming 2027 lies), and **trust gaming** (authoritative sources treated as infallible). The architecture must **explicitly model uncertainty, decay, and hardware-specificity**—not just store structured data.

**The system must be a *decision-support engine*, not a knowledge base.**
Its core should be an **evidence graph** (immutable, versioned) + **knowledge graph** (structured, typed relationships) + **decision graph** (actionable, traceable). **RAG is a trap here**—it will drown the system in noise before it scales. **Structured querying over the graph is the only viable path.**

**The most dangerous flaw:** The proposal assumes that "structured knowledge" is enough. It is not. **The Institution needs *validated, hardware-specific, temporally-aware, and actionable* intelligence.** Without explicit modeling of **conditions, dependencies, and invalidation criteria**, the system will produce **false confidence**—the most insidious failure mode.

---

---

---

## **2. WHAT WE ARE ACTUALLY BUILDING**

We are **not** building:
- A documentation system
- A search engine
- A RAG pipeline
- A static knowledge base

We **are** building:
- A **real-time, self-correcting intelligence engine** that turns **noisy, heterogeneous, rapidly-changing source material** into **reliable, Institution-specific engineering decisions**.
- A **living model of The Institution’s technical reality**, where every claim is:
  - **Attributed** (to a source, version, and hardware context)
  - **Validated** (by experiments, benchmarks, or Institution tests)
  - **Temporally bounded** (with explicit decay and supersession)
  - **Actionable** (linked to decisions, tests, or architectural changes)
  - **Falsifiable** (with clear invalidation criteria)

This is **closer to a scientific research lab** than a library. The output is **not knowledge—it is *engineering guidance with proven provenance***.

---

---

---

## **3. WHAT IS RIGHT ABOUT THE CURRENT PROPOSAL**

✅ **Source immutability** (Raw Source Archive) is critical. Never lose the original.
✅ **Structured extraction** (not just summaries) is the only way to avoid noise.
✅ **"Store once, render many views"** is correct—**but only if the stored data is *semantically rich***.
✅ **The video is the source; the discovery is the unit of intelligence** is a strong principle.
✅ **Distinction between general knowledge, hypotheses, and validated findings** is essential.
✅ **Provenance chain** (Decision → Validation → Evidence → Source) is non-negotiable.

---
---
---
## **4. WHAT IS WRONG / MISSING / DANGEROUS**

### **🔴 Conceptual Flaws**
- **No explicit model of uncertainty.**
  - The system must track **confidence intervals, conflicting evidence, and conditions** for every claim.
  - Example: *"Prefix caching improves throughput by 20% (90% confidence, validated on A100s, contradicted by [Source X] on 5060 Ti)."*

- **No temporal decay mechanism.**
  - A claim from 2026-01 must **automatically lose weight** unless re-validated.
  - **Hardware/software versioning must be first-class citizens.**

- **No hardware-specific validation layer.**
  - A result from a quad-3090 **must not** propagate to The Institution’s 5060 Ti without explicit testing.
  - **General Knowledge ≠ Institution Truth.**

- **No conflict resolution system.**
  - If 30 sources disagree on prefix caching, the system must **flag, cluster, and adjudicate**—not just store all 30.

- **"Structured knowledge" is vague.**
  - What are the **fields**? What are the **relationships**? How do we **merge, split, or deprecate** knowledge objects?

### **🔴 Architectural Risks**
- **RAG is a scalability trap.**
  - RAG over raw sources = **noise amplification**.
  - RAG over structured knowledge = **still too coarse** (loses relationships, conditions, and provenance).
  - **→ Replace with structured graph queries.**

- **No explicit "invalidation" mechanism.**
  - If new evidence contradicts an old claim, the system must:
    1. **Detect the conflict** (automated).
    2. **Flag the old claim** (as "superseded" or "contradicted").
    3. **Trigger re-validation** (if critical to The Institution).

- **No protection against AI worker poisoning.**
  - If multiple Hermes instances or specialist models contribute, they could:
    - **Hallucinate** (add false knowledge).
    - **Reinforce biases** (circular confirmation).
    - **Corrupt provenance** (lose source attribution).
  - **→ Need: Role-based access, sandboxed edits, and automated conflict detection.**

- **No "actionability" enforcement.**
  - Every knowledge object must **explicitly answer**:
    - *What should The Institution do?*
    - *What should we test?*
    - *What happens if we ignore this?*
  - **If it doesn’t, it’s not intelligence—it’s just data.**

- **No scale-aware automation strategy.**
  - At 10,000 sources, **manual curation is impossible**.
  - **Automation must handle:**
    - Extraction (AI)
    - Clustering (AI)
    - Conflict detection (AI)
    - Decay marking (automated)
    - **But human judgment must stay in the loop for high-impact decisions.**

### **🔴 Hidden Failure Modes**
| **Failure Mode**               | **Risk**                                                                 | **Example**                                                                 |
|--------------------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **Information Collapse**       | 30 videos on prefix caching → 30 disconnected facts.                     | No synthesis, no clustering, no conflict resolution.                    |
| **Temporal Poisoning**         | Old, invalidated knowledge treated as current.                         | A 2025 optimization technique fails in 2026 but isn’t flagged.          |
| **Trust Gaming**               | Over-reliance on "authoritative" sources without context.             | A YouTuber’s demo is treated as equivalent to a peer-reviewed benchmark.   |
| **Hardware Misgeneralization** | Cloud results applied to local 5060 Ti without validation.           | Proxmox/LXC configs from AWS assumed to work on bare metal.               |
| **Provenance Loss**            | Edits by AI workers lose source attribution.                          | A synthesized claim can’t be traced back to its original video.           |
| **Circular Reinforcement**    | AI workers confirm each other’s hallucinations.                        | Two Hermes instances agree on a false optimization, reinforcing it.      |
| **Scale Paralysis**            | System becomes too large for AI or humans to use effectively.          | 100,000 knowledge objects, but no way to prioritize or filter.           |
| **Decision Drift**             | Decisions aren’t traceable to evidence.                               | No one can answer: *"Why did we choose this architecture?"*               |

---
---
---
## **5. RECOMMENDED INFORMATION MODEL**

### **Core Model: The *Evidence-Knowledge-Decision Graph***
A **typed, directed graph** where nodes are **immutable objects** and edges represent **validated relationships**.

#### **Node Types (Immutable)**
| **Type**               | **Description**                                                                 | **Example**                                                                 |
|------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **Source**             | Raw material (video, transcript, GitHub issue, benchmark). **Never modified.** | YouTube video: *"llama.cpp MTP Deep Dive (2026-07-20)"*                  |
| **Evidence**           | Atomic, verifiable claim extracted from a source. **Attributed + timestamped.** | *"MTP improves throughput by 30% on 4090 (Source: [Video X], 2026-07-20)"* |
| **Knowledge Object**   | Synthesized, structured intelligence. **Versioned + hardware-context-aware.** | *"Prefix caching (v2) → 20% throughput gain (A100, validated; 5060 Ti: untested)"* |
| **Hypothesis**         | Testable prediction or architectural consideration. **Linked to evidence.** | *"MTP + prefix caching may reduce latency by 15% (needs validation)"*     |
| **Experiment**         | Validation attempt (success/failure). **Reproducible + conditions logged.** | *"Tested MTP on 5060 Ti: 12% throughput gain (2026-09-01, [Config Y])*      |
| **Decision**           | Institution adoption/rejection of a knowledge object. **Provenance chain.** | *"Adopt MTP for local inference (Decision #42, based on Experiment #1234)"* |
| **Invalidation**       | Explicit contradiction or supersession of prior knowledge. **Triggers re-validation.** | *"MTP v2 supersedes MTP v1 (Source: [Release Notes], 2026-08-15)"*      |

#### **Edge Types (Relationships)**
| **Edge Type**               | **Description**                                                                 | **Example**                                                                 |
|-----------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **Extracted From**          | Evidence → Source (provenance).                                               | Evidence #567 → Source #12 (YouTube video)                                |
| **Supports**                | Evidence → Knowledge Object (corroboration).                                  | Evidence #567 → Knowledge Object #89 (Prefix caching v2)                 |
| **Contradicts**             | Evidence/Knowledge Object → Evidence/Knowledge Object. **Triggers conflict resolution.** | Knowledge Object #89 → Evidence #568 (contradictory benchmark)             |
| **Validated By**            | Knowledge Object → Experiment (confirmation).                                | Knowledge Object #89 → Experiment #1234 (5060 Ti test)                   |
| **Depends On**              | Knowledge Object → Knowledge Object (prerequisites).                         | Knowledge Object #89 (MTP) → Knowledge Object #456 (KV-cache config)     |
| **Superseded By**           | Knowledge Object → Knowledge Object (temporal replacement).                 | Knowledge Object #89 (MTP v1) → Knowledge Object #90 (MTP v2)             |
| **Affects**                 | Knowledge Object → Subsystem/Architecture (impact scope).                     | Knowledge Object #89 → Subsystem: Inference Pipeline                      |
| **Requires Hardware**       | Knowledge Object → Hardware Spec (contextual constraint).                     | Knowledge Object #89 → Hardware: A100 (40GB VRAM)                         |
| **Decision For**            | Decision → Knowledge Object (adoption/rejection).                            | Decision #42 → Knowledge Object #89 (Adopt MTP)                          |

---
---
---
## **6. RECOMMENDED DATA / KNOWLEDGE STRUCTURE**

### **Schema for Each Node Type**
#### **📌 Source**
```yaml
id: "src_20260908_001"
type: "video" | "document" | "github_issue" | "benchmark" | "release_notes"
title: "llama.cpp MTP Deep Dive"
url: "https://youtube.com/..."
author: "AI Engineer X"
date: "2026-07-20"
version: "1.0"  # If applicable (e.g., model version)
hardware_context: null | "A100" | "5060 Ti" | "Proxmox LXC"
tags: ["llama.cpp", "MTP", "performance"]
raw_content: "..."  # Immutable transcript/markdown
checksum: "sha256:..."  # For tamper-proofing
```

#### **🔍 Evidence**
```yaml
id: "ev_20260908_001"
source_id: "src_20260908_001"
type: "claim" | "benchmark" | "configuration" | "failure_mode"
text: "MTP improves throughput by 30% on 4090 with 128K context"
confidence: 0.9  # 0-1, based on source trust + reproducibility
hardware_context: "RTX 4090 (24GB VRAM)"
software_context: "llama.cpp v0.12.3, CUDA 12.2"
conditions: "128K context, batch_size=1"
date_extracted: "2026-09-08"
extracted_by: "AI Agent #3" | "Human: Mike D."
validation_status: "unvalidated" | "corroborated" | "contradicted" | "superseded"
```

#### **🧠 Knowledge Object**
```yaml
id: "ko_20260908_001"
title: "Prefix Caching (v2)"
description: "Reduces redundant KV-cache computation for repeated prompts"
type: "technique" | "configuration" | "failure_mode" | "architectural_primitive"
domain: "inference" | "training" | "deployment" | "monitoring"
status: "active" | "deprecated" | "experimental"
version: "2"  # Incremented on major changes
hardware_applicability: ["A100", "H100", "5060 Ti (untested)"]  # Explicit list
software_applicability: ["llama.cpp >=0.12", "vLLM"]
conditions: "context_size > 32K, batch_size > 1"
performance_impact: "+20% throughput (A100), +12% (5060 Ti, estimated)"
risks: "May increase VRAM usage by 10%"
related_evidence: ["ev_20260908_001", "ev_20260908_002"]
related_knowledge: ["ko_20260908_002 (MTP)", "ko_20260908_003 (KV-cache config)"]
confidence: 0.85  # Weighted average of supporting evidence
last_validated: "2026-09-01" | null
validation_status: "theoretical" | "corroborated" | "institution_validated" | "contradicted"
institution_specific: true | false  # Has this been tested on our hardware?
```

#### **🔬 Hypothesis**
```yaml
id: "hyp_20260908_001"
text: "MTP + prefix caching may reduce latency by 15% in multi-user scenarios"
knowledge_object_id: "ko_20260908_001"  # Linked to relevant KO
priority: "high" | "medium" | "low"  # Based on potential impact
testability: "high" | "medium" | "low"  # How easy is it to validate?
conditions: "multi-user, batch_size=4, 5060 Ti"
proposed_test: "Run benchmark with/without MTP+prefix caching, measure latency"
status: "open" | "testing" | "validated" | "rejected"
```

#### **🧪 Experiment**
```yaml
id: "exp_20260908_001"
hypothesis_id: "hyp_20260908_001"
title: "MTP + Prefix Caching Latency Test"
description: "Benchmark on 5060 Ti with batch_size=4"
hardware: "RTX 5060 Ti (16GB VRAM)"
software: "llama.cpp v0.12.3, CUDA 12.2"
configuration: "MTP enabled, prefix caching v2, context=64K"
results: {
  baseline_latency_ms: 120,
  test_latency_ms: 102,
  improvement: "15% reduction",
  notes: "VRAM usage increased by 8%"
}
date: "2026-09-05"
performed_by: "AI Agent #5" | "Human: Mike D."
reproducible: true | false
status: "success" | "failure" | "inconclusive"
```

#### **⚖️ Decision**
```yaml
id: "dec_20260908_001"
knowledge_object_id: "ko_20260908_001"
title: "Adopt Prefix Caching (v2) for Local Inference"
decision: "adopt" | "reject" | "defer" | "monitor"
rationale: "15% latency improvement validated on 5060 Ti (exp_20260908_001). VRAM increase acceptable."
confidence: 0.9  # Based on validation strength
conditions: "Only for batch_size > 1, context > 32K"
hardware_scope: ["5060 Ti", "A100 (future)"]  # Explicit list
software_scope: ["llama.cpp >=0.12"]
depends_on: ["dec_20260908_002 (KV-cache config)"]  # Other decisions
invalidation_criteria: "If VRAM usage exceeds 14GB or latency regresses >5%"
date: "2026-09-08"
made_by: "Council of Wise Ones"
```

#### **❌ Invalidation**
```yaml
id: "inv_20260908_001"
invalidates: "ko_20260908_001"  # Knowledge Object being superseded
superseded_by: "ko_20260908_090"  # Newer KO
reason: "New benchmark (ev_20260908_090) shows MTP v3 outperforms v2 by 25%"
date: "2026-09-08"
source: "ev_20260908_090"  # Evidence triggering invalidation
```

---
---
---
## **7. HOW RAW INFORMATION BECOMES INTELLIGENCE**

### **The Intelligence Pipeline**
```
RAW SOURCES
   │
   ▼
🔹 **Extraction** (AI Agents)
   - Transcribe videos, parse docs, scrape GitHub.
   - **Output:** Evidence nodes (atomic, attributed claims).
   - **Automation:** 100% (AI can do this reliably).
   │
   ▼
🔹 **Deduplication & Clustering** (AI Agents)
   - Group similar evidence (e.g., 30 prefix caching videos → 1 cluster).
   - **Output:** Candidate Knowledge Objects.
   - **Automation:** 90% (AI + lightweight human review for edge cases).
   │
   ▼
🔹 **Conflict Detection & Resolution** (AI + Human)
   - Flag contradictions (e.g., "MTP helps" vs. "MTP hurts").
   - **Output:** Resolved Knowledge Objects (or marked as "conflicted").
   - **Automation:** 70% (AI detects, humans adjudicate high-impact conflicts).
   │
   ▼
🔹 **Knowledge Object Creation** (AI + Human)
   - Synthesize evidence into structured, versioned KOs.
   - **Fields:** Title, description, conditions, hardware/software scope, confidence.
   - **Automation:** 60% (AI drafts, humans refine critical KOs).
   │
   ▼
🔹 **Hypothesis Generation** (AI Agents)
   - Propose testable predictions (e.g., "MTP + prefix caching → 15% latency drop").
   - **Output:** Hypothesis nodes.
   - **Automation:** 80% (AI generates, humans prioritize).
   │
   ▼
🔹 **Validation Queue** (Automated + Human)
   - Prioritize hypotheses for testing (based on impact, testability, confidence).
   - **Output:** Experiment nodes.
   - **Automation:** 50% (AI ranks, humans approve high-priority tests).
   │
   ▼
🔹 **Institution Validation** (Human + AI)
   - Run experiments on **The Institution’s hardware**.
   - **Output:** Validated/Rejected Knowledge Objects.
   - **Automation:** 30% (AI assists with test design, humans execute).
   │
   ▼
🔹 **Decision Layer** (Council of Wise Ones)
   - Adopt/reject/defer knowledge based on validated findings.
   - **Output:** Decision nodes (with full provenance).
   - **Automation:** 0% (Human-only, but AI provides briefings).
   │
   ▼
🔹 **Compiled Intelligence** (Automated)
   - Distill validated KOs + Decisions into **actionable briefs** for AI agents.
   - **Output:** JSON/YAML context files for planning/build agents.
   - **Automation:** 100% (AI generates, humans audit).
   │
   ▼
🔹 **Field Knowledge** (Human)
   - Long-form synthesis for human consumption (e.g., "Inference Optimization Guide").
   - **Output:** Markdown/PDF reports.
   - **Automation:** 10% (AI drafts, humans edit).
```

---
---
---
## **8. HOW CROSS-SOURCE SYNTHESIS SHOULD WORK**

### **The Synthesis Engine**
**Goal:** Turn **N sources on the same topic** into **1-3 Knowledge Objects** (not N disconnected facts).

#### **Step 1: Clustering**
- **Input:** 30 evidence nodes about "prefix caching."
- **Action:** Group by **semantic similarity** (e.g., "implementation details," "benchmarks," "failure modes").
- **Output:** 3-5 clusters (e.g., "Prefix Caching v1," "Prefix Caching v2," "Hardware Limitations").

#### **Step 2: Conflict Detection**
- **Input:** Cluster of 10 evidence nodes.
- **Action:** Check for:
  - **Direct contradictions** (e.g., "improves throughput" vs. "degrades throughput").
  - **Contextual differences** (e.g., "works on A100" vs. "fails on 5060 Ti").
  - **Temporal changes** (e.g., "v1 works" vs. "v2 supersedes v1").
- **Output:** Flagged conflicts + proposed resolutions.

#### **Step 3: Knowledge Object Creation**
- **Input:** Resolved cluster.
- **Action:** Create a **single Knowledge Object** with:
  - **Title:** "Prefix Caching (v2)"
  - **Description:** Synthesized explanation.
  - **Conditions:** "context > 32K, batch_size > 1"
  - **Hardware Applicability:** ["A100 (validated)", "5060 Ti (theoretical)"]
  - **Confidence:** Weighted average of supporting evidence.
  - **Related Evidence:** Links to all 10 source evidence nodes.
  - **Related Knowledge:** Links to MTP, KV-cache, etc.

#### **Step 4: Versioning & Supersession**
- If new evidence contradicts or improves the KO:
  - **Increment version** (e.g., "Prefix Caching v3").
  - **Add invalidation edge** (v2 → v3).
  - **Mark v2 as "superseded"** (but keep for provenance).

#### **Step 5: Institution-Specific Validation**
- **Action:** Test KO on **The Institution’s hardware**.
- **Output:**
  - If validated → `institution_validated: true`.
  - If failed → Create **new evidence** ("Prefix Caching v2 fails on 5060 Ti under load").

---
---
---
## **9. HOW TEMPORAL CHANGE SHOULD WORK**

### **Temporal Model: "Knowledge Decay"**
Every Knowledge Object and Evidence node must have:
1. **`date_extracted`** (When the claim was first captured).
2. **`last_validated`** (When it was last confirmed).
3. **`expiry_date`** (Automatically calculated based on domain velocity).
4. **`version`** (Incremented on major updates).
5. **`superseded_by`** (Pointer to newer KO).

#### **Decay Rules**
| **Domain**          | **Half-Life** | **Expiry Policy**                                                                 |
|---------------------|---------------|-----------------------------------------------------------------------------------|
| Model Releases      | 6 months      | Auto-flag as "stale" if not re-validated.                                         |
| Hardware Benchmarks | 12 months     | Auto-deprecate if hardware is >2 generations old.                                |
| Software Libraries  | 3 months      | Auto-flag if new major version released.                                         |
| Architectural Primitive | 18 months | Manual review required annually.                                               |
| Failure Modes       | 24 months     | Assume still relevant unless contradicted.                                       |

#### **Automated Temporal Triggers**
- **Stale Flag:** If `last_validated` > `expiry_date`, mark as `"stale"`.
- **Supersession Check:** If new KO with same `title` but higher `version` exists, auto-link via `superseded_by`.
- **Conflict Alert:** If new evidence **contradicts** an old KO, flag for human review.

#### **Manual Overrides**
- **Council can:**
  - Extend expiry for **foundational knowledge** (e.g., "Proxmox LXC basics").
  - Force re-validation for **critical KOs** (e.g., "KV-cache configuration").
  - Mark KOs as **"timeless"** (e.g., "How TCP works").

---
---
---
## **10. HOW TRUST / EVIDENCE SHOULD WORK**

### **Trust is *Multi-Dimensional***
**Do NOT use a single "trust score."** Instead, model trust as a **vector of attributes**:

| **Attribute**          | **Description**                                                                 | **Weight** | **Example Values**                          |
|------------------------|---------------------------------------------------------------------------------|------------|---------------------------------------------|
| **Source Type**        | How authoritative is the source?                                                | 0.3        | `peer_reviewed=1.0`, `youtube=0.6`, `forum=0.3` |
| **Recency**            | How recent is the information?                                                 | 0.2        | `days_old < 30 → 1.0`, `> 365 → 0.1`         |
| **Reproducibility**    | Can the claim be independently verified?                                       | 0.25       | `reproducible=1.0`, `anecdotal=0.2`          |
| **Independence**       | Are multiple sources saying the same thing?                                    | 0.15       | `3+ independent sources → 1.0`              |
| **Technical Depth**    | Does the source provide rigorous details?                                       | 0.1        | `benchmark_data=1.0`, `opinion=0.3`          |
| **Hardware Match**     | Does the evidence apply to The Institution’s hardware?                          | 0.3        | `5060 Ti=1.0`, `A100=0.5`, `cloud=0.2`       |
| **Institution Test**   | Has The Institution validated this internally?                                | 0.5        | `validated=1.0`, `untested=0.0`              |

#### **Composite Trust Score**
```
trust_score = Σ (attribute_value * weight)
```
- **Range:** 0 (useless) to 1 (highly reliable).
- **Usage:**
  - **Evidence nodes:** Used to weight contributions to Knowledge Objects.
  - **Knowledge Objects:** Used to prioritize validation and decisions.
  - **Decisions:** Only KOs with `trust_score >= 0.8` can be adopted without further testing.

#### **Trust Thresholds**
| **Trust Score** | **Action**                                                                 |
|-----------------|---------------------------------------------------------------------------|
| ≥ 0.9           | **Auto-promote to "high confidence"** (but still needs Institution validation for critical decisions). |
| 0.7 - 0.89      | **Flag for priority validation**.                                        |
| 0.5 - 0.69      | **Include in knowledge base, but mark as "low confidence"**.            |
| < 0.5           | **Exclude from decisions** (but keep in archive for reference).         |

#### **Dynamic Trust Adjustments**
- **Corroboration:** If 3+ independent sources agree, **boost independence score**.
- **Contradiction:** If a high-trust source contradicts a KO, **trigger re-validation**.
- **Hardware Mismatch:** If evidence is from `A100` but The Institution uses `5060 Ti`, **penalize hardware match score**.

---
---
---
## **11. HOW INSTITUTION VALIDATION SHOULD WORK**

### **The Validation Hierarchy**
```
GENERAL KNOWLEDGE (Lowest)
   │
   ▼
INSTITUTION HYPOTHESIS (Middle)
   │
   ▼
INSTITUTION VALIDATED KNOWLEDGE (Highest)
```

#### **🌍 General Knowledge**
- **Definition:** Claims from external sources, **not yet tested by The Institution**.
- **Trust Floor:** `trust_score >= 0.5` (minimum to enter the system).
- **Usage:**
  - Can be **referenced** in hypotheses.
  - **Cannot** directly inform decisions without validation.
- **Example:** *"MTP improves throughput (YouTube, A100, trust=0.7)"*

#### **🔬 Institution Hypothesis**
- **Definition:** A **testable prediction** derived from General Knowledge.
- **Creation:**
  - AI agents propose hypotheses from high-trust KOs.
  - Council prioritizes based on **impact, testability, and confidence**.
- **Status:**
  - `open` (not yet tested)
  - `testing` (experiment in progress)
  - `validated` → Promoted to **Institution Validated Knowledge**.
  - `rejected` → Marked as **false for The Institution**.
- **Example:** *"Hypothesis: MTP + prefix caching will reduce latency by 15% on 5060 Ti."*

#### **✅ Institution Validated Knowledge**
- **Definition:** Knowledge **confirmed by The Institution’s own tests**.
- **Requirements:**
  - Must have **≥1 successful experiment** on **Institution hardware**.
  - Must have **`institution_specific: true`**.
  - Must have **`trust_score >= 0.9`** (after validation).
- **Usage:**
  - **Directly informs decisions**.
  - **Highest priority for compiled intelligence**.
- **Example:** *"Prefix Caching v2 → 12% latency reduction on 5060 Ti (validated 2026-09-01)."*

#### **Validation Workflow**
1. **Hypothesis Selection:**
   - AI ranks hypotheses by:
     - `trust_score` of supporting KOs.
     - `impact` (potential benefit to The Institution).
     - `testability` (ease of validation).
   - Council approves high-priority hypotheses.

2. **Experiment Design:**
   - AI generates **test plan** (hardware, software, metrics, success criteria).
   - Human reviews for **feasibility and safety**.

3. **Execution:**
   - Run on **Institution hardware** (5060 Ti, Proxmox, etc.).
   - Log **full configuration, results, and anomalies**.

4. **Outcome:**
   - **Success:** Promote KO to **Institution Validated Knowledge**.
   - **Failure:** Create **new evidence** ("Hypothesis X failed on 5060 Ti due to Y").
   - **Inconclusive:** Retry with adjusted parameters.

---
---
---
## **12. HOW DECISIONS SHOULD BE REPRESENTED**

### **Decision Nodes: Full Provenance Chain**
Every decision must be **traceable back to its roots**:

```
DECISION
   │
   ├── **Rationale** (Why was this chosen?)
   ├── **Confidence** (0-1, based on validation strength)
   ├── **Conditions** (When does this apply?)
   ├── **Scope** (Hardware/software boundaries)
   ├── **Depends On** (Other decisions or KOs)
   ├── **Invalidation Criteria** (What would make this wrong?)
   │
   ▼
VALIDATION (Experiment #1234)
   │
   ├── **Results** (Quantitative/qualitative)
   ├── **Hardware/Software Context**
   │
   ▼
HYPOTHESIS (Hypothesis #567)
   │
   ├── **Text** (What was being tested?)
   ├── **Supporting Knowledge Objects**
   │
   ▼
KNOWLEDGE OBJECT (KO #89)
   │
   ├── **Title/Description**
   ├── **Evidence** (Source #12, Source #34)
   │
   ▼
EVIDENCE (Evidence #567)
   │
   ├── **Claim** ("MTP improves throughput by 30%")
   ├── **Source** (YouTube Video #12)
```

#### **Decision Schema (Expanded)**
```yaml
id: "dec_20260908_001"
title: "Adopt Prefix Caching v2 for Local Inference"
decision: "adopt" | "reject" | "defer" | "monitor"
knowledge_object_id: "ko_20260908_001"
hypothesis_id: "hyp_20260908_001"  # Optional, if decision was hypothesis-driven
experiment_id: "exp_20260908_001"   # The validation that justified this
rationale: |
  "Prefix Caching v2 reduced latency by 12% on 5060 Ti (exp_20260908_001).
  VRAM increase (8%) is acceptable given our 16GB capacity.
  Supersedes dec_20260801_045 (Prefix Caching v1)."
confidence: 0.95  # Based on validation strength
conditions: |
  - Only for batch_size > 1
  - Context size > 32K
  - llm.cpp >= 0.12
hardware_scope: ["5060 Ti", "Future A100"]  # Explicit list
software_scope: ["llama.cpp", "vLLM"]       # Explicit list
depends_on: ["dec_20260908_002 (KV-cache config)"]  # Other decisions
invalidation_criteria: |
  - Latency regresses >5%
  - VRAM usage exceeds 14GB
  - New KO supersedes ko_20260908_001
date: "2026-09-08"
made_by: "Council of Wise Ones"
status: "active" | "superseded" | "reversed"
reversed_by: null | "dec_20261001_001"  # If later overturned
```

#### **Decision Querying**
The system must support **reverse provenance queries**:
- *"Why did we adopt Prefix Caching v2?"* → Returns full chain to sources.
- *"What decisions depend on KO #89?"* → Returns all downstream decisions.
- *"What would invalidate Decision #42?"* → Returns `invalidation_criteria`.

---
---
---
## **13. HOW ACTIONABILITY SHOULD WORK**

### **The Actionability Checklist**
**Every Knowledge Object and Decision must answer:**

| **Question**                          | **Field in Schema**               | **Example**                                                                 |
|---------------------------------------|-----------------------------------|-----------------------------------------------------------------------------|
| What is it?                           | `title`, `description`             | "Prefix Caching v2: Reduces redundant KV-cache computation."              |
| Why does it matter?                   | `impact`                          | "12% latency reduction → faster multi-user inference."                   |
| What does it affect?                  | `affects` (subsystems)             | ["Inference Pipeline", "Memory Usage"]                                   |
| What evidence supports it?           | `related_evidence`                | ["ev_20260908_001 (YouTube)", "exp_20260908_001 (5060 Ti test)"]           |
| How confident are we?                 | `confidence`                      | 0.95 (validated on our hardware)                                          |
| What conditions does it depend on?    | `conditions`                      | "batch_size > 1, context > 32K"                                           |
| What could invalidate it?            | `invalidation_criteria`           | "VRAM > 14GB or latency regression >5%"                                   |
| What should The Institution do?      | `recommended_action`              | "Enable in llama.cpp config for all batch inference."                     |
| What should we test?                  | `hypothesis_id` / `proposed_test` | "Test MTP + prefix caching combo (hyp_20260908_002)."                     |
| What happens if we ignore it?         | `risk_of_inaction`                | "20% slower multi-user inference → degraded user experience."            |

#### **Actionability Enforcement**
- **Automated Checks:**
  - If any of these fields are **missing or vague**, flag the KO/Decision as **"not actionable"**.
  - Example: A KO with no `recommended_action` or `invalidation_criteria` is **rejected**.
- **Human Review:**
  - Council **must** approve all high-impact decisions for **actionability completeness**.

---
---
---
## **14. HOW THE SYSTEM SHOULD SCALE**

### **Scale-Aware Design Principles**
| **Scale**       | **Sources** | **Automation %** | **Human Role**                          | **Key Challenges**                          |
|-----------------|-------------|------------------|----------------------------------------|---------------------------------------------|
| **Small**       | 10-100      | 50%              | Full curation                          | Avoiding premature over-engineering.       |
| **Medium**      | 100-1,000   | 70%              | Conflict resolution, high-impact KOs   | Deduplication, clustering.                  |
| **Large**       | 1,000-10,000| 85%              | Trust model tuning, decision approval   | Temporal decay, hardware-specific filtering.|
| **Massive**     | 10,000+     | 95%              | Auditing, failure mode analysis         | AI worker coordination, provenance loss.   |

### **Automation Breakdown**
| **Task**                          | **Small Scale** | **Medium Scale** | **Large Scale** | **Massive Scale** |
|-----------------------------------|-----------------|------------------|-----------------|-------------------|
| Extraction                        | AI              | AI               | AI              | AI                |
| Deduplication/Clustering          | AI + Human      | AI               | AI              | AI                |
| Conflict Detection                | Human           | AI + Human       | AI              | AI                |
| Knowledge Object Creation        | Human           | AI + Human       | AI              | AI                |
| Hypothesis Generation             | Human           | AI               | AI              | AI                |
| Validation Prioritization        | Human           | AI + Human       | AI              | AI                |
| Institution Validation            | Human           | Human            | Human + AI      | Human + AI        |
| Decision Making                   | Human           | Human            | Human           | Human             |
| Compiled Intelligence Generation | Human           | AI               | AI              | AI                |
| Temporal Decay Marking            | Manual          | Automated        | Automated       | Automated         |
| Trust Scoring                     | Manual          | Automated        | Automated       | Automated         |

### **Critical Scale Safeguards**
1. **Modular Design:**
   - Each layer (Extraction, Synthesis, Validation, Decisions) must be **independently scalable**.
   - Example: Extraction can run on a separate cluster from Validation.

2. **Incremental Processing:**
   - **Batch processing** for new sources (e.g., "process 100 videos/day").
   - **No full re-indexing**—only update affected KOs.

3. **Query Optimization:**
   - **Graph database** (Neo4j, Dgraph) for fast traversal of relationships.
   - **Vector search** (for semantic similarity in clustering).
   - **Caching** for frequently accessed KOs (e.g., "current best practices").

4. **Storage Efficiency:**
   - **Compress raw sources** (but keep checksums for verification).
   - **Delta encoding** for KO versions (store only changes, not full copies).

5. **AI Worker Coordination:**
   - **Role-based access** (Extractors, Synthesizers, Validators).
   - **Conflict-free replicated data types (CRDTs)** to prevent edit collisions.
   - **Sandboxed edits** (new KOs start as "draft" until reviewed).

---
---
---
## **15. HOW MULTIPLE AI WORKERS SHOULD INTERACT WITH IT**

### **Worker Roles & Permissions**
| **Role**               | **Permissions**                          | **Example Tasks**                          | **Risk Mitigation**                          |
|------------------------|------------------------------------------|--------------------------------------------|---------------------------------------------|
| **Extractor**          | Read Sources, Create Evidence            | Transcribe videos, parse GitHub issues.    | No write access to KOs/Decisions.            |
| **Synthesizer**        | Read Evidence, Create/Edit KOs           | Cluster evidence, draft KOs.               | Edits are **sandboxed** (require approval).  |
| **Validator**          | Read KOs, Create Experiments             | Design tests, flag conflicts.             | Experiments are **logged immutably**.        |
| **Decision Assistant** | Read KOs/Experiments, Propose Decisions  | Generate decision briefings.              | **No direct decision-making** (human-only).|
| **Auditor**            | Read All, Flag Anomalies                 | Detect hallucinations, trust gaming.      | **Read-only** (except for flagging).         |

### **Worker Coordination Rules**
1. **No Direct Edits to Decisions:**
   - AI workers can **propose** decisions, but **only humans can approve**.

2. **Sandboxed Knowledge Objects:**
   - New KOs start as **"draft"** (visible only to the creating worker).
   - Must pass **automated checks** (actionability, provenance) before promotion.
   - **Human review** required for high-impact KOs.

3. **Conflict Resolution:**
   - If two workers edit the same KO:
     - **Versioning:** Both edits are preserved as separate versions.
     - **Flagging:** System flags the conflict for human resolution.
     - **Merge Suggestions:** AI proposes a merged version.

4. **Provenance Tracking:**
   - Every edit must include:
     - `worker_id` (which AI/human made the change).
     - `timestamp`.
     - `rationale` (why the edit was made).

5. **Hallucination Prevention:**
   - **All AI-generated KOs** must include:
     - `source_evidence` (links to supporting evidence).
     - `confidence` (auto-calculated from trust scores).
   - **If `confidence < 0.7`**, KO is **automatically flagged** for review.

6. **Circular Reinforcement Protection:**
   - **No AI worker can validate its own hypotheses.**
   - **Validation experiments** must be designed by **different workers** than those who proposed the hypothesis.

7. **Rate Limiting:**
   - Workers have **quotas** (e.g., "100 KOs/day") to prevent spam.
   - **Exponential backoff** if too many low-confidence KOs are created.

---
---
---
## **16. WHETHER / WHEN TO USE RAG**

### **❌ Avoid RAG Initially**
- **Problem:** RAG over raw sources = **noise amplification**.
  - Example: RAG on 10,000 videos → **hallucinated summaries** with no provenance.
- **Problem:** RAG loses **relationships, conditions, and trust scores**.

### **⚠️ Limited RAG Later (If At All)**
| **Use Case**               | **RAG Over**               | **Risk**                          | **Recommendation**                          |
|----------------------------|----------------------------|-----------------------------------|---------------------------------------------|
| Raw Source Search          | Raw transcripts/docs        | High (noise, no structure)       | **Avoid.** Use structured queries instead.  |
| Knowledge Object Search    | Structured KOs              | Medium (loses relationships)     | **Only if graph queries are insufficient.** |
| Decision Briefings         | Compiled Intelligence       | Low (pre-filtered)                | **Acceptable for human consumption.**      |
| AI Agent Context Loading   | Compiled Intelligence       | Low (if KOs are high-quality)     | **Prefer graph queries.**                   |

### **✅ Recommended Alternative: Structured Graph Queries**
- **Example Query:**
  ```cypher
  MATCH (ko:KnowledgeObject {title: "Prefix Caching"})
  WHERE ko.institution_specific = true
  AND ko.hardware_applicability CONTAINS "5060 Ti"
  RETURN ko, ko.confidence, ko.last_validated
  ORDER BY ko.confidence DESC
  ```
- **Advantages:**
  - **Precise** (no hallucinations).
  - **Provenance-preserving** (returns full KO with evidence links).
  - **Filterable** (by hardware, recency, trust, etc.).

### **RAG Workflow (If Absolutely Necessary)**
1. **Pre-filter sources** (by recency, trust, relevance).
2. **Extract structured data first** (don’t RAG on raw text).
3. **Use RAG only for summarization** (not for decision-making).
4. **Always include citations** (link back to source KOs/Evidence).

---
---
---
## **17. WHAT SHOULD BE AUTOMATED**

| **Task**                          | **Automation Level** | **Tools/Methods**                          | **Human Oversight**               |
|-----------------------------------|----------------------|--------------------------------------------|-----------------------------------|
| Source Ingestion                  | 100%                 | YouTube API, GitHub webhooks, RSS feeds.   | None.                             |
| Transcription                     | 100%                 | Whisper, local STT models.                 | None.                             |
| Evidence Extraction               | 95%                  | LLMs with structured prompts.             | Spot-check 5% of outputs.         |
| Deduplication/Clustering          | 90%                  | Embeddings + vector search.               | Review clusters for false merges.|
| Conflict Detection                | 80%                  | Graph traversal for contradictions.        | Adjudicate high-impact conflicts.|
| Knowledge Object Drafting        | 70%                  | LLM synthesis from evidence clusters.     | Edit critical KOs.                |
| Hypothesis Generation            | 80%                  | LLM + pattern recognition.                | Prioritize hypotheses.           |
| Validation Queue Ranking         | 70%                  | Impact * confidence * testability.         | Approve top 10%.                  |
| Temporal Decay Marking            | 100%                 | Automated based on domain half-life.       | None.                             |
| Trust Scoring                     | 90%                  | Weighted vector calculations.             | Tune weights periodically.       |
| Compiled Intelligence Generation | 100%                 | LLM distilling validated KOs/Decisions.    | Audit for completeness.           |
| Experiment Design                 | 60%                  | LLM + template-based test plans.          | Review for safety/feasibility.   |

---
---
---
## **18. WHAT SHOULD REQUIRE HUMAN JUDGEMENT**

| **Task**                          | **Why Human?**                          | **Frequency**               | **Who?**                          |
|-----------------------------------|-----------------------------------------|----------------------------|-----------------------------------|
| High-Impact KO Approval           | Risk of misinformation.                 | Daily                       | Council of Wise Ones.             |
| Decision Making                   | Accountability for Institution direction.| Weekly                      | Council.                          |
| Conflict Resolution (High Trust) | Nuanced technical judgment.            | As needed                   | Domain experts.                  |
| Trust Model Tuning               | Calibrating weights for new domains.    | Monthly                     | Council + AI Auditors.            |
| Failure Mode Analysis             | Identifying systemic risks.             | Quarterly                   | Council.                          |
| Hardware Validation               | Ensuring tests match Institution setup. | Per experiment              | Mike D. / Technical Lead.         |
| AI Worker Auditing                | Detecting hallucination patterns.       | Weekly                      | Auditor AI + Human.              |
| System Architecture Changes       | Long-term strategic decisions.          | Rare                        | Council.                          |

---
---
---
## **19. MINIMUM VIABLE VERSION (MVV)**

### **Phase 1: Foundation (0-3 Months)**
**Goal:** Prove the pipeline works with **10-100 sources**.

#### **Components:**
1. **Raw Source Archive:**
   - Store transcripts, metadata, checksums.
   - **Tools:** Local database (SQLite), Git for versioning.

2. **Extraction Layer:**
   - LLM-based evidence extraction (structured prompts).
   - **Output:** Evidence nodes (JSON).

3. **Basic Knowledge Graph:**
   - Manual KO creation (no automation yet).
   - **Schema:** Title, description, evidence links, confidence.
   - **Storage:** Neo4j or Dgraph (for relationships).

4. **Validation Queue:**
   - Simple prioritization (manual ranking).
   - **Output:** List of hypotheses to test.

5. **Decision Record:**
   - Manual decision logging (Markdown/JSON).
   - **Provenance:** Link decisions to KOs/Evidence.

6. **Compiled Intelligence:**
   - Manual briefings for AI agents (JSON context files).

#### **Automation:**
- **Extraction:** 100% automated (LLM).
- **Clustering:** Manual (no AI yet).
- **Validation:** 100% human.

#### **Scale:**
- **Sources:** 10-100 videos/docs.
- **Workers:** 1-2 AI extractors, 1 human curator.

#### **Success Criteria:**
- **10 sources → 50 evidence nodes → 20 KOs → 5 validated findings → 2 decisions.**
- **No provenance loss** (every KO traces to source).
- **No hallucinations** (all KOs have evidence links).

---
---
---
## **20. LONG-TERM VERSION (12+ Months)**

### **Phase 2: Scaling (3-12 Months)**
**Goal:** Handle **1,000-10,000 sources** with **70% automation**.

#### **Additions:**
1. **Automated Clustering:**
   - Vector embeddings + community detection (Louvain algorithm).
   - **Output:** Candidate KOs (human review for merges).

2. **Conflict Detection:**
   - Graph-based contradiction finding.
   - **Output:** Flagged KOs for human resolution.

3. **Temporal Decay:**
   - Automated stale marking.
   - **Output:** Weekly reports of outdated KOs.

4. **Trust Scoring:**
   - Multi-dimensional trust vectors.
   - **Output:** KO prioritization for validation.

5. **Multi-AI Worker Coordination:**
   - Role-based access (Extractors, Synthesizers, Validators).
   - **Output:** Sandboxed edits, provenance tracking.

#### **Automation:**
- **Extraction/Clustering:** 100% automated.
- **KO Creation:** 70% automated (AI drafts, human reviews).
- **Validation:** 30% automated (AI designs tests, humans execute).

#### **Scale:**
- **Sources:** 1,000-10,000.
- **Workers:** 5-10 AI agents, 2-3 human curators.

---
### **Phase 3: Full Autonomy (12+ Months)**
**Goal:** **100,000+ sources** with **95% automation**.

#### **Additions:**
1. **Self-Improving Loop:**
   - Gap detection → Research → Experiment → Knowledge Update.
   - **Output:** Automated hypothesis generation and testing.

2. **Hardware-Specific Validation Pipelines:**
   - CI/CD for experiments (GitHub Actions, local runners).
   - **Output:** Automated test execution on Institution hardware.

3. **Decision Automation:**
   - AI proposes decisions (human approval for high-impact).
   - **Output:** Draft decisions with full provenance.

4. **Adaptive Trust Model:**
   - Dynamic weight tuning based on validation outcomes.
   - **Output:** Self-calibrating trust scores.

5. **Distributed Graph Database:**
   - Sharded storage for scale (e.g., Dgraph cluster).
   - **Output:** Sub-second query responses.

#### **Automation:**
- **Extraction/Synthesis/Validation:** 95% automated.
- **Decision Proposals:** 80% automated (human approval for critical).
- **Auditing:** 100% automated (AI + human spot-checks).

#### **Scale:**
- **Sources:** 100,000+.
- **Workers:** 20+ AI agents, 1-2 human overseers.

---
---
---
## **21. FAILURE MODES WE MUST DESIGN AGAINST**

| **Failure Mode**               | **Risk**                                                                 | **Mitigation**                                                                 |
|--------------------------------|--------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| **Information Collapse**       | 30 videos → 30 disconnected facts.                                        | **Clustering + Synthesis** (force merging of similar evidence).              |
| **Temporal Poisoning**         | Old knowledge treated as current.                                        | **Automated decay marking + expiry dates.**                                  |
| **Trust Gaming**               | Over-reliance on "authoritative" but wrong sources.                     | **Multi-dimensional trust vectors** (no single score).                      |
| **Hardware Misgeneralization** | Cloud/A100 results applied to 5060 Ti.                                  | **Explicit hardware scope in KOs + Institution validation required.**       |
| **Provenance Loss**            | Edits lose source attribution.                                           | **Immutable evidence nodes + edit logging.**                                |
| **Circular Reinforcement**    | AI workers confirm each other’s hallucinations.                         | **Sandboxed edits + cross-worker validation.**                               |
| **Scale Paralysis**            | System too large to be useful.                                           | **Modular design + query optimization + incremental processing.**          |
| **Decision Drift**             | Decisions untraceable to evidence.                                       | **Full provenance chain (Decision → KO → Evidence → Source).**             |
| **Hallucinated Knowledge**     | AI workers invent false KOs.                                            | **All KOs must link to evidence + confidence scoring.**                      |
| **Validation Bypass**          | General Knowledge used for decisions without testing.                    | **Strict hierarchy: General → Hypothesis → Validated.**                     |
| **AI Worker Poisoning**        | Malicious/compromised worker corrupts the corpus.                       | **Role-based access + rate limiting + audit logs.**                         |
| **Overfitting to Sources**     | System mirrors biases of input data.                                    | **Diverse source intake + conflict detection.**                             |
| **Knowledge Bloat**            | Too many low-value KOs clutter the system.                               | **Automated pruning of stale/low-confidence KOs.**                           |
| **False Confidence**           | High-confidence KOs that are wrong.                                      | **Mandatory Institution validation for high-impact KOs.**                  |
| **Query Overload**             | Graph queries become too slow at scale.                                  | **Caching + indexing + sharding.**                                           |

---
---
---
## **22. CONCRETE RECOMMENDATION**

### **🏗️ Architecture Summary**
1. **Core Model:** **Evidence-Knowledge-Decision Graph** (typed, directed, immutable nodes).
2. **Storage:**
   - **Raw Sources:** Immutable (Git + SQLite).
   - **Graph Data:** Neo4j or Dgraph (for relationships).
   - **Vector Index:** FAISS or Weaviate (for clustering).
3. **Pipeline:**
   - **Extraction → Clustering → Synthesis → Validation → Decisions → Compiled Intelligence.**
4. **Automation:**
   - **100%:** Extraction, clustering, temporal decay.
   - **70-90%:** KO creation, hypothesis generation, trust scoring.
   - **0%:** Decision-making (human-only).
5. **RAG:** **Avoid initially.** Use **structured graph queries** instead.
6. **Multi-AI Workers:**
   - **Role-based access** (Extractors, Synthesizers, Validators).
   - **Sandboxed edits** (draft KOs require approval).
   - **Conflict detection** (automated flagging).

### **🚀 Immediate Next Steps**
1. **Build the MVV (Phase 1):**
   - Implement **Raw Source Archive + Extraction Layer** (10-100 sources).
   - Manually create **10-20 KOs** to test the schema.
   - Validate **1-2 decisions** with full provenance.

2. **Test the Graph Model:**
   - Use **Neo4j** to prototype relationships (KO → Evidence → Source).
   - Ensure **provenance queries** work (e.g., "Why was this decision made?").

3. **Develop Trust Vectors:**
   - Start with **source type, recency, reproducibility**.
   - Tune weights based on early validation outcomes.

4. **Design the Validation Pipeline:**
   - Create **templates for experiments** (hardware, software, metrics).
   - Run **1-2 Institution-specific tests** (e.g., prefix caching on 5060 Ti).

5. **Avoid RAG:**
   - **Do not** build a RAG pipeline yet. Focus on **structured queries**.

### **🎯 Long-Term Vision**
- **Self-Improving Intelligence Engine:**
  - **Gap Detection:** AI identifies missing knowledge (e.g., "No data on MTP + 5060 Ti").
  - **Automated Research:** AI proposes new sources to ingest.
  - **Experiment Design:** AI generates test plans for validation.
  - **Knowledge Update:** AI incorporates new findings into KOs.

- **Hardware-Aware Intelligence:**
  - **Every KO** is tagged with **applicable hardware/software**.
  - **Automated filtering** (e.g., "Show me KOs validated on 5060 Ti").

- **Decision Auditability:**
  - **Any decision** can be **fully traced** to its roots in **< 30 seconds**.

---
---
---
## **23. QUESTIONS WE HAVE NOT ASKED BUT SHOULD**

### **🔍 Strategic Questions**
1. **How do we handle *proprietary* or *closed-source* information?**
   - Example: NVIDIA’s internal optimizations for 5060 Ti.
   - **Risk:** We may miss critical knowledge.
   - **Proposal:** Flag as `"source_type: proprietary"` with lower trust.

2. **What if a *high-trust source* is later proven wrong?**
   - Example: A peer-reviewed paper on MTP is debunked.
   - **Risk:** Temporal poisoning of the entire corpus.
   - **Proposal:** **Retroactive invalidation** (mark all KOs derived from it as `"re-evaluate"`).

3. **How do we detect *unknown unknowns*?**
   - Example: A new technique (e.g., "Quantum KV-Cache") emerges that we’re not tracking.
   - **Risk:** The system becomes **outdated without realizing it**.
   - **Proposal:** **Gap detection AI** (analyzes patterns to suggest missing domains).

4. **How do we prevent the system from becoming an *echo chamber*?**
   - Example: All sources are from the same community (e.g., only llama.cpp developers).
   - **Risk:** **Bias reinforcement** (missing alternative viewpoints).
   - **Proposal:** **Diverse source intake** + **conflict-seeking AI** (actively looks for contradictory evidence).

5. **What is the *ROI* of this system?**
   - How do we **measure** its value to The Institution?
   - **Proposal:** Track **decision impact** (e.g., "Adopting KO #89 saved $X in hardware costs").

### **🛠️ Technical Questions**
6. **How do we handle *non-English* sources?**
   - **Risk:** Missing critical knowledge from non-English communities.
   - **Proposal:** **Automated translation + human review** for high-trust sources.

7. **How do we *version* the entire corpus?**
   - Example: If we change the KO schema, how do we migrate old data?
   - **Risk:** **Schema drift** breaks queries.
   - **Proposal:** **Immutable snapshots** (like Git commits) + **backward-compatible migrations**.

8. **How do we *backup* and *restore* the graph?**
   - **Risk:** Data loss would be catastrophic.
   - **Proposal:** **Daily backups** + **checksum verification**.

9. **How do we *test* the system itself?**
   - Example: How do we know the **synthesis engine** isn’t merging unrelated KOs?
   - **Risk:** **Silent failures** (bad intelligence looks good).
   - **Proposal:** **Synthetic benchmarks** (e.g., inject known contradictions and check detection).

### **🤖 AI-Specific Questions**
10. **How do we *calibrate* AI workers?**
    - Example: If an AI extractor has a 10% hallucination rate, how do we detect and correct it?
    - **Risk:** **Undetected hallucinations** corrupt the corpus.
    - **Proposal:** **Spot-check audits** + **worker-specific trust scores**.

11. **How do we *prioritize* AI worker tasks?**
    - Example: Should Extractors focus on **new sources** or **re-processing old ones**?
    - **Risk:** **Inefficient resource use**.
    - **Proposal:** **Dynamic task queue** (prioritize high-impact, high-trust sources).

12. **How do we *retire* old AI workers?**
    - Example: If a new LLM is better at extraction, how do we transition?
    - **Risk:** **Inconsistent knowledge** from different worker generations.
    - **Proposal:** **Versioned worker outputs** + **re-processing old data** with new models.

---
---
---
---
## **🏁 FINAL ANSWER: THE INSTITUTION’S INTELLIGENCE ARCHITECTURE**

### **We are building a *Decision-Support Engine*, not a knowledge base.**
Its architecture must:
1. **Model uncertainty, decay, and hardware-specificity explicitly.**
2. **Enforce a strict hierarchy:**
   `Source → Evidence → Knowledge Object → Hypothesis → Experiment → Decision`.
3. **Use a typed graph database** (not RAG) for **provenance-preserving queries**.
4. **Automate extraction, clustering, and conflict detection**—but **keep humans in the loop for validation and decisions**.
5. **Treat every claim as *guilty until validated on Institution hardware*.**
6. **Design for scale from day one** (modular, query-optimized, immutable).

### **The most dangerous mistakes to avoid:**
- **Assuming RAG is sufficient** (it will drown you in noise).
- **Ignoring temporal decay** (old truths become lies).
- **Skipping Institution-specific validation** (cloud results ≠ local results).
- **Allowing AI workers to self-validate** (circular reinforcement).
- **Losing provenance** (the system must always answer *"Why?"*).

### **Start small, but think big.**
- **MVV:** 10 sources → 50 evidence → 20 KOs → 5 decisions.
- **Long-term:** 100,000+ sources → **self-correcting, hardware-aware, actionable intelligence**.

**The Institution’s competitive edge will come from:**
✅ **Faster validation** (hardware-specific testing).
✅ **Better synthesis** (clustering + conflict resolution).
✅ **Reliable provenance** (every decision traces to evidence).
✅ **Adaptive trust** (dynamic, multi-dimensional scoring).

**If we get this right, the system will not just *store* knowledge—it will *generate* better engineering decisions than any human or AI could alone.**

---
---
**Council Member:** *Mike D.*
**Date:** *2026-09-08*
**Verdict:** *Proceed with the Evidence-Knowledge-Decision Graph architecture. Avoid RAG. Prioritize provenance, validation, and hardware-specificity.*
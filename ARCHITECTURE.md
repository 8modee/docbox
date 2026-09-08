# THE INSTITUTION — ENGINEERING INTELLIGENCE SYSTEM
## Architecture Specification & Proof-of-System Plan (Deliverables A–N)

- **VERSION:** 1.0-draft-for-review
- **DATE:** 2026-09-09
- **STATUS:** AWAITING REVIEW/APPROVAL. No implementation has started (operating rule 14).
- **SCOPE:** Smallest rigorous system that proves the full intelligence pipeline on ONE real source (DigitalSpaceport video `L8xTCd80p68`), with a credible expansion path.

---

## 1. ARCHITECTURAL VERDICT (Deliverable A)

**We are building an append-only, provenance-first knowledge ledger with derived views and a decision chain — not a graph database, not a RAG system, not a markdown knowledge base.**

One sentence: *records are immutable events; knowledge is a graph of records; everything a human reads is generated.*

What the system IS, concretely:

1. **An append-only record store** — JSONL files, one per entity type. Records are never edited or deleted; corrections and supersessions are *new records linked to old ones*. Git provides tamper-evidence and history for free.
2. **A content-addressed claim layer** — claim identity is a hash of normalized semantic content + conditions. Duplicate detection and false-corroboration defence fall out of identity design rather than policing.
3. **An explicit relationship layer** — corroboration, contradiction, supersession, derivation (relay lineage) are themselves records with mandatory rationale.
4. **A strict epistemic boundary** — EXTERNAL (source → evidence → claim → position) and INSTITUTION (gap → hypothesis → experiment → result → finding → decision → action) are disjoint subgraphs connected only by defined bridge edges. A quad-3090 claim can never masquerade as a 5060 Ti finding.
5. **A view generator** — every human-readable artefact (briefs, queues, ledgers) is a pure function of the store, regenerable at will. Nothing human-facing is hand-maintained.
6. **A CLI** (`intel.py`, stdlib Python) as the only write path — schema validation, referential integrity and dedup are enforced at ingest, before a bad record can enter.

What we deliberately are NOT building (and why):

| Rejected for now | Why | Trigger to revisit |
|---|---|---|
| Neo4j / graph DB | Relations-as-records gives us the graph with zero infrastructure; a graph DB adds an operation burden before we have 10 sources | When relation traversal is too slow at ~50k claims |
| SQLite as primary store | A binary store defeats human auditability, git diff-ability, and multi-agent merge semantics — the three properties the council demanded | See ADR-001; re-evaluate at ~50k claims |
| Embeddings / RAG | Structured provenance answers known questions; retrieval is for *discovery* and can be added later behind one interface without touching the model | After ≥100 sources, when recall-by-search becomes the bottleneck |
| Trust scores / probability math | Fake numeric confidence hides conditionality; multidimensional qualitative assessment is more honest and more useful | Never, unless a concrete decision failure shows a gap |
| Agent swarms, CRDTs, auto-modifying architecture | Multi-writer safety is achieved by append-only + single validated write path + content hashing | When concurrent write contention is actually observed |

**Critical evaluation of the conceptual model in the mission:** the pipeline shape (evidence → claims → positions → hypothesis → experiment → finding → decision) is confirmed as the *spine*. Two corrections are made: (1) relationships are promoted from an implied layer to first-class records, because source independence and contradiction are relationship properties, not entity properties; (2) TOPIC and GAP are promoted to first-class records (the diagram treats them as implicit), because positions hang off topics and the research queue hangs off gaps — both need identity and lifecycle.

---

## 2. INFORMATION MODEL (Deliverable B)

### 2.1 Entity inventory

| Entity | ID scheme | Immutable? | One-line definition |
|---|---|---|---|
| SOURCE | `SRC-####` + content hash | yes | An external artifact (video, doc, repo, release notes, post) |
| EVIDENCE | `EVD-####` + locator hash | yes | A locatable span inside a source (quote + locator) |
| CLAIM | content hash `CLM-<h12>` | yes | One atomic external proposition with full conditions |
| TOPIC | slug (`tp-<slug>`) | mutable, curated | A stable subject of interest that claims/positions hang off |
| RELATION | hash of (from,type,to,rationale) | yes | Typed edge: corroborates / contradicts / supersedes / derives-from / duplicates / refines / transfers-to |
| POSITION | `POS-<topic>-v<n>` | yes (versioned) | Current conditioned synthesis of claims for a topic |
| GAP | `GAP-##` | yes + status | An explicit “we don’t know”, with why-it-matters |
| HYPOTHESIS | `HYP-##` | yes + status | Institution-testable proposition with pre-registered success criteria |
| EXPERIMENT | `EXP-##` | yes + status | Planned/run validation on Institution hardware, with protocol |
| RESULT | `RES-##` | yes | Raw measured outcome + artifact paths |
| FINDING | `FND-##` | yes | Interpretation of results vs hypothesis criteria |
| DECISION | `DEC-##` | yes | Adopt/reject/defer/watch choice with mandatory provenance |
| ACTION | `ACT-##` | yes | Concrete executed change traceable to a decision |

This is the mission’s list with three changes: RELATIONSHIP → RELATION (record, not attribute); TOPIC and GAP made first-class; **ATTestation is folded into RELATION** (`corroborates`) rather than being a separate entity.

### 2.2 Common record envelope

Every record in every JSONL file carries:

```json
{
  "id": "CLM-ab12cd34ef56",
  "type": "claim",
  "schema_version": 1,
  "created_at": "2026-09-09T12:00:00Z",
  "created_by": "worker-id (e.g. zcode-glm53 / hermes-01 / human:mdiak)",
  "body": { ...type-specific... }
}
```

### 2.3 The four entities that carry the system

**CLAIM** — atomic unit of external knowledge. Body:

```json
{
  "subject": "llama.cpp speculative decoding (MTP)",
  "predicate": "increases decode throughput",
  "object": {"value": "up to ~3x", "unit": "relative"},
  "modality": "relayed",
  "conditions": {
    "hardware": null,
    "software": {"name": "llama.cpp", "version": null},
    "model": null,
    "workload": null,
    "config": null
  },
  "evidence_refs": ["EVD-0031"],
  "lineage": {"relayed_from_claim": null, "original_source_hint": "Unsloth post, unidentified"},
  "topics": ["tp-speculative-decoding"],
  "temporal": {"observed_at": null, "asserted_at": "2026-09 (video pub date)"},
  "status": "active"
}
```

Rules: one predicate per claim (atomicity, lint-enforced). `null` conditions are *allowed and preserved* — an unspecified condition is information (“we don’t know the workload”), never silently filled. `modality` ∈ {measured, demonstrated, asserted, relayed, speculated} — this is the honesty axis. `status` transitions are appended to an in-record `status_history` (the only mutable field; everything else append-only).

**EVIDENCE** — the auditable tether. Body: `source_id`, `locator` (e.g. transcript timestamp range `[266s–277s]` / line range / URL anchor), `quote` (verbatim), `capture_quality` (auto-caption / manual / screenshot / raw text), `garble_flags` (known caption corruption). Integrity check: quote must fuzzy-match the archived source text at the locator — this kills hallucinated locators at ingest.

**POSITION** — versioned synthesis. Body: `topic`, `statement` (must be conditioned — carries its conditions inline, not as an afterthought), `supporting_claims`, `contradicting_claims`, `assessment` (the multidimensional narrative from §3, not a number), `institution_status` ∈ {untested, validated, contradicted, partially-validated}, `valid_as_of`, `supersedes`, `drafted_by`, `approved_by`. Positions never overwrite; `POS-tp-x-v2` supersedes `v1`, both remain queryable.

**DECISION** — the reason future workers can ask “why”. Body: `choice` (adopt/reject/defer/watch + specifics), `basis_refs` (findings and/or positions — **mandatory, schema-enforced**), `alternatives_considered`, `rationale`, `decided_by`, `decided_at`, `review_conditions` (what would reopen this). A decision whose basis_refs don’t resolve cannot be written.

### 2.4 Lifecycle summary

- Everything is created once, never mutated (except claim/record `status` + `status_history`).
- Correction = new record + `supersedes`/`revises` relation. Obsolescence = `supersedes` relation. Nothing is deleted.
- Human review gates: TOPIC creation, POSITION approval, DECISION approval. Everything else is machine-ingestible with validation. (This is the minimum review surface that prevents “hallucination → fact” while not drowning the human.)

---

## 3. EVIDENCE / TRUST MODEL (Deliverable C)

No truth score. Instead, four orthogonal, explicitly recorded dimensions:

1. **Modality** (on the claim): was it *measured*, *demonstrated on-screen*, *asserted*, *relayed second-hand*, or *speculated*? A relayed “up to 3×” and a direct benchmark are not the same class of object and the record makes that visible everywhere the claim appears.
2. **Lineage / independence** (computed, not assumed): each RELATION `derives-from` between claims, plus source linkage, lets the system compute **independent corroboration count** = number of supporting claims whose lineage roots are distinct AND between which no derivation edge exists. Creator A observes; B repeats A; C quotes B ⇒ 3 claims, corroboration weight **1**. This is the structural defence against fake corroboration.
3. **Condition specificity** (lint-checked): how much of {hardware, model, version, workload, config} is specified? “3× on nothing specified” scores lower on transferability than “1.8× measured on 2×3090, 27B FP16, long-context” — without either becoming a number pretending to be truth.
4. **Institution relevance / validation state** (on the position): external strength is always reported *separately from* Institution validation: **“Strong external evidence (one direct measurement, one independent relay); UNTESTED on 5060 Ti 16GB.”** The two halves are never merged into one score.

Source authority is a small curated enum on SOURCE (e.g. `practitioner-primary`, `official-docs`, `vendor`, `community-secondhand`) with a one-line rationale — enough to rank reading and validation priority, not enough to silently outrank contradictory measurement.

---

## 4. SYNTHESIS MODEL — claims → positions (Deliverable D)

- A position MUST cite explicit claim IDs (supporting and contradicting). A script refuses to generate/validate a position whose citations don’t resolve or whose statement lacks any condition clause.
- **Contradictions are inputs to synthesis, not noise.** When claims conflict, synthesis must either (a) explain the split via condition-delta (“A measured on Ampere FP16; B on Blackwell W4 — conditions differ, both may be right”), producing a *conditioned* position, or (b) record the contradiction as UNEXPLAINED, expose it in the CONTRADICTIONS view and in GAPs. Silently picking a winner is forbidden (rule 5).
- Positions are drafted by a worker (LLM) and human-approved in v1. Because every position cites its claims, positions are *auditable and regenerable in principle* — a new worker can re-derive and diff them.
- Changing evidence ⇒ new position version with `supersedes`; the old version survives, so “what did we believe in March and why” is always answerable.

---

## 5. INSTITUTION MODEL — external → internal (Deliverable E)

```
EXTERNAL WORLD                    INSTITUTION WORLD
SOURCE → EVIDENCE → CLAIM → POSITION ──origin_of──→ HYPOTHESIS
                                            ↓
                              (pre-registered success criteria)
                                            ↓
                                     EXPERIMENT → RESULT → FINDING
                                            ↓                   │
                                         DECISION ←──validates/contradicts (bridge)
                                            ↓
                                          ACTION → new config → new operational data
                                                   └──(becomes Institution EVIDENCE)──┘
```

Boundary rules (schema-enforced):
- A FINDING must reference an EXPERIMENT; an EXPERIMENT must reference a HYPOTHESIS with pre-registered criteria. You cannot manufacture a “finding” directly from an external claim.
- External claims enter the Institution world *only* as hypothesis premises.
- Institution experimental results may enter the external layer as claims — tagged `modality: measured`, `conditions.hardware: institution-5060ti-16gb`, evidence = RESULT artifacts. Our own results get exactly the same treatment as external ones.
- Transfers between hardware contexts are RELATION `transfers-to` with rationale — a transfer is a hypothesis, never an implication.

---

## 6. TEMPORAL MODEL (Deliverable F)

- Three clocks on every chain: `published_at` (world), `observed_at`/`asserted_at` (evidence), `created_at` (ledger). Version scope is structured (`software.name/version`, `model`, `hardware`) — not prose.
- Claims never expire. A claim becomes obsolete only via an explicit `supersedes` relation (usually from a newer claim or a finding), so “deprecated approaches” remain represented and searchable — they are history, not garbage (rule 6).
- Positions carry `valid_as_of`; views support `--as-of <date>` to render the world as it was believed then (proof-of-system implements this for the timeline view only).
- **Volatility class** on TOPIC (`fast` / `slow`) drives a staleness report: a `fast` topic position whose youngest supporting claim is >90 days old appears in the brief’s REVALIDATION section. This is a triage signal, not an automatic invalidation.

---

## 7. MULTI-WORKER MODEL (Deliverable G)

Threat model: multiple Hermes instances / agents contributing claims. Failure modes to prevent: silent overwrite, duplicate claims, provenance corruption, hallucination → fact, circular corroboration, document drift.

Mechanisms (all cheap, all structural):
1. **Single write path**: workers only add via `intel.py add-*`. Direct file editing of the ledger is out of contract (git diff makes violations visible).
2. **Append-only files** — no worker can overwrite another’s record by construction. Concurrent appends use an OS file lock inside `intel.py` (single-line JSON records make append atomicity achievable). *Recorded uncertainty:* at real multi-worker concurrency we may move to per-worker shard files merged by a collector; deferred until contention is observed.
3. **Content-addressed claim IDs**: before insert, the normalized-content hash is looked up. Match ⇒ the new evidence is appended as a `corroborates`/`derives-from` RELATION to the existing claim, not as a second claim. Duplication is converted into corroboration automatically, and lineage analysis then judges whether that corroboration is independent (§3.2).
4. **Ingest-time integrity** (the anti-hallucination gate): evidence locators must resolve and quotes must fuzzy-match the archived source; every `*_refs` field must resolve to an existing record or the add fails. There is no code path that writes an unanchored claim.
5. **Worker identity on every record** (`created_by`) + git commit per batch ⇒ full attribution and tamper-evidence.
6. **Views are generated** ⇒ no hand-edited documents to drift; stale views are a non-issue because they cost nothing to regenerate.

---

## 8. RETRIEVAL MODEL (Deliverable H)

For the proof-of-system:
- **Structured queries** (exact IDs, topic, source, status, modality, date range, hardware tag) — these answer known questions.
- **Raw material stays archived and greppable** (`source-archive/<id>/…`) — evidence rediscovery is `grep` over transcripts with locators.
- **No embeddings yet.** One interface (`search(query) -> [record ids]`) is reserved in the CLI so a semantic index over evidence spans can be added later *as an index*, never as the source of truth (council point 13). Trigger: ≥100 sources or demonstrated recall failure.

---

## 9. VIEW MODEL (Deliverable I)

Principle: **STORE ONCE. DERIVE MANY.** Every view is `ledger → deterministic markdown`, carries `generated_at` + ledger content hash, and is regenerated on demand.

Implemented in proof-of-system (4): `INTELLIGENCE-BRIEF` (top positions + alerts + open gaps), `VALIDATION-QUEUE` (open hypotheses ranked by topic volatility × relevance), `CONTRADICTIONS` (unexplained only), `WHAT-CHANGED` (ledger delta since date).

Specified, deferred (9): `TOPIC-BRIEF`, `DECISION-LEDGER`, `SOURCE-AUDIT`, `GAPS`, `FOUNDATION-ALERTS`, `KNOWN-FAILURE-MODES`, `MODEL-STRATEGY`, `ARCHITECTURE-IMPLICATIONS`, `HISTORICAL-TIMELINE`. Each is a small pure function; none introduces new storage.

---

## 10. EVALUATION MODEL (Deliverable J)

The mission’s ten questions become `tests/test_eval.py` (pytest), each failing loudly:

1. **Provenance** — every claim → evidence → source → locator resolves; every quote fuzzy-matches archived text at its locator.
2. **Duplication** — ingest a paraphrase of an existing claim ⇒ no new claim ID; a `corroborates` relation is created.
3. **Contradiction** — contradictory pair persists; relation recorded; appears in CONTRADICTIONS view; neither silently wins.
4. **Independence** — fixture A asserts / B relays A / C relays B ⇒ independent corroboration count == 1 (not 3).
5. **Temporality** — after a superseding claim, position renders new statement; old position + old claim retrievable via `--as-of`.
6. **Hardware transfer** — quad-3090 claim renders `institution_status: untested`; schema rejects any DECISION citing it without an intervening FINDING.
7. **Actionability** — running the golden path yields a VALIDATION-QUEUE entry with topic, criteria sketch and priority.
8. **Decision traceability** — walk DEC→FND→EXP→HYP→POS→CLM→EVD→SRC; every hop resolves.
9. **Compression** — TOPIC-BRIEF for speculative decoding fits within a token budget while retaining conditions and the open contradiction.
10. **Adversarial** — claim with fabricated evidence locator ⇒ rejected; “Institution-validated” wrapper over an external claim with no experiment ⇒ rejected; hallucinated corroborator (self-citing loop) ⇒ flagged by lineage check.

Test data: the real anchor transcript for 1–8; synthetic fixtures for 4 and 10 (we will not wait for real bad actors).

---

## 11. MINIMUM VIABLE IMPLEMENTATION (Deliverable K)

```
intelligence-system/
  ARCHITECTURE.md            ← this document
  DECISIONS.md               ← ADRs + recorded uncertainties
  ledger/                    ← 13 JSONL files (one per entity)
  archive/                   ← immutable raw material (copy of L8xTCd80p68 transcript; pointer doc for existing location)
  schemas/                   ← JSON Schema, one per record type, versioned
  intel.py                   ← CLI: add-source|add-evidence|add-claim|link|add-position|add-gap|add-hypothesis|add-experiment|add-result|add-finding|add-decision|add-action | query | view | check | asof
  views.py                   ← view generators (pure functions)
  tests/                     ← eval harness (§10) + schema/integrity tests
```

Stdlib-only Python 3.14 (json, hashlib, argparse, filelock via msvcrt/fcntl fallback, difuzzmatch via difflib). No dependencies = no supply-chain surface, runs anywhere Hermes runs.

**Golden path (the actual proof):** populate the ledger from the already-archived anchor video and its existing scan (`local-ai-intelligence/foundation-impact.md`):

1. `SRC-0001` = the video; archive copy with hash. `EVD-000x` for each alert/FI, with timestamp locators and verbatim quotes (e.g. ALERT-1 ⇒ `[266–277s]` “I read an Unsloth tweet that you can expect up to 3x faster performance”, garble-flagged, `modality: relayed`).
2. ~18 claims (3 alerts + 15 FIs), topics created for the ~8 recurring subjects (speculative decoding, prefix caching, quantization-vs-agentic-workload, parser config, LXC-vs-VM, concurrency limits, VLM workflows, model routing). Contradiction relations for T-1 and T-2 with unexplained deltas → GAPs.
3. Positions drafted for `tp-speculative-decoding` and `tp-quantization-agentic-workload` citing claim IDs; institution_status = untested.
4. Institution chain for the strongest thread: `GAP-001` (identify Unsloth primary post + llama.cpp PRs) → `HYP-001` (“recent llama.cpp speculative decoding yields ≥1.5× decode vs current build on 5060 Ti 16GB, Qwen3-class quant, representative agentic workload” — criteria pre-registered) → `EXP-001` (protocol recorded, **not run**) → `DEC-001` = **DEFER llama.cpp serving-stack migration; WATCH releases** (basis: POS + external evidence only, review conditions set) → `ACT-001` (validation-queue entry created).
5. Views regenerated; eval harness run green.

That demonstrates every arrow of the mission diagram on real data while violating none of the operating rules (no experiment is faked; no claim is promoted to fact).

---

## 12. FUTURE EXPANSION PATH (Deliverable L)

| Scale | What changes | What does NOT change |
|---|---|---|
| 10 → 100 sources | One ingest adapter per source type (YouTube, docs crawler, release-notes fetcher, repo watcher) — all emitting the same 13 record types | Schemas, IDs, relations, views |
| 100 → 1,000 | Add `search()` implementation (evidence-span embeddings); topic taxonomy curation becomes a scheduled task | Storage layer; append-only contract |
| 1,000 → 10,000 | Query layer re-implemented over SQLite/DuckDB **as an index over the same JSONL** (or migrate store; same records); lineage computation cached | Record schemas; relations; positions |
| 10,000 → 100,000+ | Real DB, incremental views, dedicated synthesis workers | Everything conceptual — this is an infrastructure migration, not a redesign |

**Must be correct NOW:** claim conditions schema; evidence locators; lineage/derivation edges; external/institution boundary; append-only discipline; content addressing.
**Designed for, built later:** retrieval, richer views, worker automation of position drafting.
**Can wait:** DB indexes, embeddings, swarm orchestration, trust formalization, auto-ingestion pipelines.

---

## 13. FAILURE MODES (Deliverable M)

How the intelligence system itself could become dangerous, misleading or useless — and the standing defence:

1. **Stale views read as truth** → views carry `generated_at` + ledger hash; regenerating is free.
2. **Hallucinated locators/quotes poison evidence** → ingest-time fuzzy-match verification (§7.4); eval test 1.
3. **False corroboration by repetition** → content-hash dedup + lineage independence (§3.2); eval test 4.
4. **Boundary erosion** (“everyone knows MTP gives 3×”) → schema-enforced basis_refs; eval tests 6, 10.
5. **Schema drift** across workers/versions → `schema_version` + validation at append; migration = new records, never in-place edits.
6. **Topic soup / taxonomy explosion** → curated topic list; aliases (incl. caption garbles: Quoin/Quinn→Qwen); new topics need approval.
7. **Claim granularity rot** (claims becoming paragraphs) → atomicity lint: one predicate per claim.
8. **Contradiction fatigue** (everything flagged) → condition-delta resolution is the default; only genuinely unexplained splits stay flagged.
9. **Provenance theater** (records exist, chains broken) → `intel.py check` + eval tests 1, 8 run on every batch.
10. **The system becomes the product** (meta-work replaces engineering) → standing rule: no new entity/relation/view without a one-paragraph ADR in DECISIONS.md; every ledger batch must end in a view or queue entry someone will actually read.
11. **Overtrust in synthesis** (position wording hardens between versions) → positions must restate conditions; lint rejects unhedged universal statements from non-measured evidence.

---

## 14. IMPLEMENTATION PLAN (Deliverable N)

| Phase | Content | Output | Gate |
|---|---|---|---|
| **0 (this document)** | Architecture spec + ADRs | ARCHITECTURE.md, DECISIONS.md | **OWNER/COUNCIL APPROVAL — system stops here until then (rule 14)** |
| 1 | Schemas + `intel.py` add/check/query + integrity core | working CLI; eval tests 1, 2, 10 pass on fixtures | `intel.py check` clean |
| 2 | Anchor-video ingest (source, evidence, ~18 claims, topics, contradiction relations) | populated external layer; tests 2, 3, 4 pass on real+fixture data | every quote verified against transcript |
| 3 | Positions + gaps + 4 view generators | INTELLIGENCE-BRIEF, VALIDATION-QUEUE, CONTRADICTIONS, WHAT-CHANGED; tests 5, 9 | views regenerate deterministically |
| 4 | Institution chain (GAP→HYP→EXP→DEC→ACT) + DECISION-LEDGER view | full golden path; tests 6, 7, 8 | decision trace walk resolves end-to-end |
| 5 | Full eval harness run + SOURCE-AUDIT + handover note | proof report | all 10 evals green; review |

Each phase is one focused session. Phases 1–4 are strictly ordered; nothing in phase N+1 may begin until phase N’s gate passes.

---

## 15. RECORDED UNCERTAINTIES (rule 12 — explicit, not silent)

1. **Single-writer lock vs per-worker shard files** for concurrent appends — lock chosen for v1; revisit at first observed contention.
2. **JSONL vs SQLite** — JSONL chosen for auditability/git; ADR-001 sets the re-evaluation trigger (~50k claims or multi-file transactional need).
3. **Quote fuzzy-match threshold** for locator verification — start strict (≥0.85 similarity, timestamps ±5s), tune on real caption noise during Phase 2.
4. **Position authorship** — human-approved in v1; if topic count grows past ~20, position *drafting* should be automated with approval retained. Not decided now.
5. **Institution-internal evidence re-entry into the external layer** — allowed in principle (§5), but the exact tagging for “our own operational data” is deferred until the first real experiment exists.

*END OF SPECIFICATION — implementation explicitly withheld pending review.*

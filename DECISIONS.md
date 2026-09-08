# THE INSTITUTION — ENGINEERING INTELLIGENCE SYSTEM
## DECISIONS — Architecture Decision Records (ADRs)

- **VERSION:** 1.0-draft-for-review
- **DATE:** 2026-09-09
- **STATUS:** AWAITING REVIEW/APPROVAL. Companion to `ARCHITECTURE.md` (same review gate). No implementation has started.
- **ADR format:** ID · Title · Status · Context · Decision · Consequences · Trigger to revisit.
- **Standing rule (ARCHITECTURE.md §13.10):** no new entity/relation/view without a one-paragraph ADR appended here.

---

### ADR-001 — Append-only JSONL as primary store (not SQLite) — ACCEPTED
- **Context:** The council demanded three properties above all: human auditability, git diff-ability, and multi-agent merge safety. A binary database defeats the first two and complicates the third. At proof-of-system scale (~tens of records) query performance is irrelevant.
- **Decision:** One JSONL file per entity type under `ledger/`; records are never edited or deleted; git provides tamper-evidence and history; `intel.py` is the only write path.
- **Consequences:** Queries are linear scans — fine at small scale. Cross-file transactions are application-level (single-process lock), not DB-level.
- **Trigger to revisit:** ~50k claims, or a demonstrated multi-file transactional need. Migration is a re-implementation of the *query/index layer over the same records* (§12), not a redesign.

### ADR-002 — Relationships are first-class records, not attributes — ACCEPTED
- **Context:** Source independence, contradiction, supersession and relay lineage are properties of the *relationship between* claims, not of either claim alone. The mission diagram implied a relationship layer without giving it identity or rationale.
- **Decision:** `RELATION` is a record type: typed edge (`corroborates` / `contradicts` / `supersedes` / `derives-from` / `duplicates` / `refines` / `transfers-to`) with mandatory rationale, hashed identity, append-only.
- **Consequences:** The knowledge graph exists without a graph database; lineage independence (§3.2) is computable from records alone. Every relation costs one line.

### ADR-003 — Content-addressed claim identity — ACCEPTED
- **Context:** Duplicate claims and false corroboration (many voices, one origin) are the two structurally most dangerous failure modes for a multi-worker ingestion system.
- **Decision:** A claim's ID is a hash of its normalized semantic content + conditions. On ingest, a hash match converts the incoming record into a `corroborates`/`derives-from` RELATION against the existing claim instead of a second claim. Independence is then judged by lineage analysis, not voice count.
- **Consequences:** Deduplication is structural (no policing needed); normalization rules for hashing must be deterministic and versioned with `schema_version`.

### ADR-004 — No numeric trust/confidence scores — ACCEPTED
- **Context:** A single number for "trust" hides exactly the information that matters for transfer decisions (what hardware, what workload, second-hand vs measured). Fake precision invites overtrust.
- **Decision:** Trust is recorded as four orthogonal dimensions (ARCHITECTURE.md §3): claim `modality`; computed lineage independence; lint-checked condition specificity; and Institution validation state reported *separately from* external strength. Source authority is a small curated enum, never allowed to silently outrank contradictory measurement.
- **Consequences:** Views are slightly more verbose; conditionality stays visible. **Trigger to revisit:** only if a concrete decision failure traces to a gap this model cannot express — not before.

### ADR-005 — ATTestation folded into RELATION (`corroborates`) — ACCEPTED
- **Context:** The mission listed attestation as a candidate entity. An attestation ("I confirm this claim") is isomorphic to a corroborating relation with a rationale; a separate entity would duplicate RELATION's identity, provenance and lifecycle.
- **Decision:** No ATTESTATION entity; corroboration is a RELATION type. Entity count settles at 13 (§2.1).

### ADR-006 — TOPIC and GAP promoted to first-class records — ACCEPTED
- **Context:** The mission diagram treated topics and gaps as implicit. But positions hang off topics (they need stable identity) and the validation queue hangs off gaps (they need status lifecycle and why-it-matters). Implicit things cannot be queried or reviewed.
- **Decision:** `TOPIC` (curated slug, volatility class, alias list incl. caption garbles) and `GAP` (explicit "we don't know" + importance + status) are record types. TOPIC creation is a human-review gate.

### ADR-007 — Stdlib-only Python CLI (`intel.py`) as sole write path — ACCEPTED
- **Context:** Workers include multiple Hermes/agent instances on heterogeneous hosts. Dependencies are a supply-chain and portability surface; ad-hoc file edits are the provenance killer.
- **Decision:** Single CLI, Python 3.14 standard library only (`json`, `hashlib`, `argparse`, `difflib`, OS file-locking). All writes go through `add-*` subcommands which enforce schema validation, referential integrity and dedup before commit.
- **Consequences:** No install friction anywhere Hermes runs; performance ceiling accepted per ADR-001 triggers.

### ADR-008 — Ingest-time evidence verification (anti-hallucination gate) — ACCEPTED
- **Context:** An LLM worker can fabricate a locator or paraphrase a quote that was never said. Downstream, everything citing that evidence is silently poisoned.
- **Decision:** `intel.py add-evidence` fails unless the locator resolves into the archived source and the quote fuzzy-matches the archived text there (initial threshold ≥0.85 similarity, timestamps ±5s). There is no code path that writes an unanchored claim.
- **Consequences:** Caption noise may cause false rejects during Phase 2 — threshold is a **recorded uncertainty** (ARCHITECTURE.md §15.3), tuned on real data, never relaxed to zero.

### ADR-009 — External/Institution boundary is schema-enforced — ACCEPTED
- **Context:** The single most expensive failure mode is a creator claim (quad-3090 rig) hardening into an operating assumption (5060 Ti 16GB build) without an experiment between them.
- **Decision:** DECISION records require `basis_refs` (schema-enforced, must resolve); FINDING requires an EXPERIMENT; EXPERIMENT requires a HYPOTHESIS with pre-registered criteria; external claims enter the Institution world only as hypothesis premises; transfers between hardware contexts are `transfers-to` relations — a hypothesis, never an implication.
- **Consequences:** "Why did we do X?" is always answerable by walking DEC→FND→EXP→HYP→POS→CLM→EVD→SRC (eval test 8). No shortcut records are permitted.

---

## RECORDED UNCERTAINTIES

The five open uncertainties (single-writer lock vs shard files; JSONL vs SQLite re-evaluation; fuzzy-match threshold; position-drafting automation; institution-internal evidence re-entry tagging) are listed with their triggers in **ARCHITECTURE.md §15** and are deliberately *not* re-decided here. Each will be promoted to a full ADR in this file at the moment it is resolved; until then they remain open items attached to the phase that surfaces them.

---

*END OF DECISIONS RECORD — Phase 0 is complete with ARCHITECTURE.md + DECISIONS.md on disk; implementation explicitly withheld pending review (operating rule 14).*

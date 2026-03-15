# Layered Stack

**Document role:** Current-state + target-state transition note  
**Authority level:** Explanatory, not normative  
**Canonical file:** `docs/layered_stack.md`  
**Last reviewed against implementation:** `2026-03-15`, packet snapshot `contract-hardening`

## Status Boundary

- `Implemented`: present in the live packet now
- `Partial`: present, but with a narrower contract than the target model
- `Planned`: accepted target, not yet implemented
- `Proposed`: analytic recommendation, not yet accepted into the packet contract

## Current Implemented Model

The live packet is a machine-joinable v2 handoff archive with six module families:

- `core/`
- `patterns/`
- `graphs/`
- `analysis/`
- `workflows/`
- `schemas/`

The packet now implements the following control-critical contracts:

- predictions join to the graph by stable `graph_node_ids`
- predictions join to overlay records by stable `overlay_ids`
- narrative clusters are first-class modules in `core/cluster_definitions.json`
- overlay IDs are first-class modules in `core/overlay_id_definitions.json`
- seeded claims are authoritative in `core/claim_registry.json`
- `schemas/module_map.json` defines typed JSON-LD imports/exports
- `schemas/validation_report.json` records dependency, registry, and edge-integrity checks across the packet

## Current Layer Map

### 1. Core registries

**Status:** `Implemented`

Authoritative records now exist for:

- cluster IDs
- overlay IDs
- claim IDs
- stable ID indexing across core packet families

Primary files:

- `core/thread_structure_inventory.json`
- `core/cluster_definitions.json`
- `core/overlay_id_definitions.json`
- `core/claim_registry.json`
- `core/id_registry.json`

### 2. Pattern library

**Status:** `Implemented`

Reusable motifs are held outside the active graph so predictions can join to patterns without collapsing the pattern library into one scenario.

Primary file:

- `patterns/pattern_library.json`

### 3. Graph layer

**Status:** `Implemented`

The packet includes:

- a dual-layer graph for current-state structure
- an exploit-chain overlay with explicit overlay IDs and graph-node mappings

Primary files:

- `graphs/iran_dual_layer_graph.json`
- `graphs/iran_exploit_chain_overlay.json`

### 4. Analysis layer

**Status:** `Implemented`

The packet includes:

- `analysis/steering_events_predictions.json`
- `analysis/information_ops_model.json`

Implemented analysis contracts:

- predictions reference graph nodes by ID
- predictions reference overlay IDs by ID
- predictions still reference reusable pattern IDs

### 5. Workflow layer

**Status:** `Partial`

The packet includes:

- `workflows/claim_scoring_template.json`
- `workflows/actor_cluster_scoring_template.json`

What is implemented:

- claim scoring joins to canonical `claim_id`
- both workflows join to canonical `cluster_id`
- workflows reference the information-operations model by explicit module dependency

What is only partial:

- the actor workflow still uses a free-form `actor` field
- there is no authoritative actor registry
- seeded actor examples are not machine-joinable by stable actor ID

### 6. Schema and validation layer

**Status:** `Implemented`

The packet includes:

- a machine-enforceable JSON-LD dependency map
- a validation report with passing dependency, registry, and edge-integrity checks across the current packet

Primary files:

- `schemas/module_map.json`
- `schemas/validation_report.json`

## Current Status Table

| Component | Status | Notes |
| --- | --- | --- |
| Cluster registry | Implemented | Canonical `cluster_id` records exist |
| Overlay ID registry | Implemented | Canonical `X-*`, `R-*`, `T-*` records exist |
| Claim registry | Implemented | Seeded workflow claim IDs are authoritative |
| Graph-to-prediction joins | Implemented | Predictions now reference `graph_node_ids` and `overlay_ids` |
| Module dependency map | Implemented | Present as a typed JSON-LD contract with explicit dependency kinds |
| Claim scoring workflow | Implemented | Canonical claim and cluster joins are present |
| Actor scoring workflow | Partial | Uses `cluster_id`, but actor identity is still free-form |
| Stable actor registry | Planned | Not present in the live packet |
| `assertion_type` split | Planned | Not encoded in current modules |
| Per-module JSON Schemas | Planned | Recommended next hardening step |
| Validator CLI | Planned | Recommended next hardening step |

## Known Gaps

### Actor identity is not normalized yet

The current packet does **not** implement an authoritative actor registry or stable actor IDs. Actor workflow inputs remain free-form labels. Canonical actor normalization is a planned hardening step, not a present invariant.

### Schema hardening is incomplete

The packet has dependency contracts and a validation report, but it does not yet have per-module JSON Schemas or a validator CLI.

## Target Hardened Model

The next hardened version should add:

1. an authoritative actor registry with stable `actor_id`
2. actor workflow conversion from free-form `actor` labels to `actor_id`
3. per-module JSON Schemas for every module family
4. a small validator CLI that enforces:
   - schema validity
   - join integrity
   - registry completeness
5. an explicit assertion split where relevant:
   - `observed`
   - `inferred`
   - `forecast`

These are target-state improvements. They are **not** current packet invariants.

## Migration Notes

- Prior competing drafts were archived under `docs/archive/`
- This file is now the only canonical layered-stack document
- Future changes should preserve the status boundary:
  - current implemented model
  - known gaps
  - target hardened model

## Clean Rule For This Docs Layer

A document may describe current state, target state, or migration plan, but it must never blend them without explicit section boundaries and status labels.

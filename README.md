# Iran Thread Handoff v2

This archive is a corrected, machine-joinable rebuild of the prior packet.

## Fixes applied

1. Predictions now reference graph nodes explicitly by `node_id` and overlay IDs explicitly by `id`.
2. Narrative clusters are first-class modules in `core/cluster_definitions.json`.
3. Overlay IDs (`X-*`, `R-*`, `T-*`) are first-class modules in `core/overlay_id_definitions.json`.
4. Seeded claim IDs are authoritative in `core/claim_registry.json`.
5. `schemas/module_map.json` is a machine-enforceable JSON-LD dependency contract with typed imports and exact exported paths.
6. `schemas/validation_report.json` covers dependency checks, registry completeness, and graph and overlay edge endpoint validity.
7. `manifest.json` uses a detached file list so the archive can validate its contents without self-hash drift.

## Resolved questions

- `module_map.json` is intended to be **machine-enforceable**, not just human guidance.
- Narrative clusters are intended to be **first-class modules**, not prose-only registry strings.

## Layout

- `core/` — authoritative registries and contracts
- `patterns/` — reusable motif library
- `graphs/` — dual-layer graph and exploit-chain overlay
- `analysis/` — predictions and information-operations model
- `workflows/` — claim and actor scoring templates
- `schemas/` — dependency contract and validation report
- `docs/` — canonical explanatory stack note and archived drafts

## Authoritative join rules

- Predictions -> graph: `analysis/steering_events_predictions.json.predictions[*].graph_node_ids[*]`
- Predictions -> overlay: `analysis/steering_events_predictions.json.predictions[*].overlay_ids[*]`
- Seeded claims -> registry: `workflows/claim_scoring_template.json.seeded_examples[*].claim_id`
- Seeded clusters -> cluster definitions: all workflow `cluster_id` fields
- Overlay -> graph: `graphs/iran_exploit_chain_overlay.json.overlay_nodes[*].mapped_graph_nodes[*]`

## Validation

`schemas/validation_report.json` currently reports `all_pass = true` across 42 structured checks, with full coverage of the 16 declared imports in `schemas/module_map.json`.

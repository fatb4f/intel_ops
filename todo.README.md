## Verdict

The archive is **substantially improved and mostly self-consistent** on the core data-join problems.

The main structural fixes are present:

* predictions now carry `graph_node_ids` and `overlay_ids` in `analysis/steering_events_predictions.json:12-27`, `80-93`, `131-142`
* clusters, overlay IDs, and claims are first-class registries in `core/cluster_definitions.json`, `core/overlay_id_definitions.json`, and `core/claim_registry.json`
* the docs layer now clearly separates current state vs target state in `docs/layered_stack.md:8-13`, `147-177`

I also checked the live data relationships directly:

* prediction → pattern / graph / overlay refs are internally consistent
* graph edges resolve to real graph nodes
* overlay edges resolve to real overlay nodes
* `core/id_registry.json` matches the current cluster / overlay / claim / graph-node / pattern sets

## Findings

### 1. Medium — `manifest.json` is not a trustworthy full integrity manifest

`manifest.json` lists hashes for the archive files, but its own recorded hash does **not** match the actual file contents, so the manifest cannot fully validate itself.
Also, the archive contains source files not listed in the manifest, including:

* `docs/layered_stack.md`
* `docs/archive/layered_stack.dtaft.md`
* `docs/archive/stack.draft.md`

That matters because `docs/layered_stack.md` is explicitly marked as the canonical layered-stack document in `docs/layered_stack.md:3-6` and `179-182`, yet it is absent from `manifest.json:6-70`.

**Why this matters:** the archive is not fully portability-safe or integrity-complete as packaged.

---

### 2. Medium — `schemas/module_map.json` still overstates “machine-enforceable” status

The packet says the module map is machine-enforceable in:

* `README.md:11`
* `README.md:16`
* `schemas/module_map.json:2-4`

But the contract still contains non-mechanical joins such as:

* `join: "index only"` in `schemas/module_map.json:37-62`
* `join: "workflow semantics"` in `schemas/module_map.json:136-163`

There are also export-name mismatches between the declared contract and the actual data shape, for example:

* `core/overlay_id_definitions.json` exports `"overlay_id"` in `schemas/module_map.json:13-17`, but the real field is `id` in `core/overlay_id_definitions.json:8-54`
* `graphs/iran_dual_layer_graph.json` exports `"edge"` in `schemas/module_map.json:73-78`, but the real collection is `edges` in `graphs/iran_dual_layer_graph.json:70-156`
* `graphs/iran_exploit_chain_overlay.json` exports `"overlay_node"` / `"overlay_edge"` in `schemas/module_map.json:81-85`, but the actual collections are `overlay_nodes` / `overlay_edges` in `graphs/iran_exploit_chain_overlay.json:3-129`

**Why this matters:** the packet is better disciplined than before, but the contract is not yet fully executable as stated.

---

### 3. Medium — `validation_report.json` does not cover the full declared contract surface

`README.md:12` and `38` plus `docs/layered_stack.md:123-125` describe the validation layer as if it covers packet joins broadly, and `schemas/validation_report.json:209-212` reports `all_pass: true` across 51 checks.

That is true for the checks that exist, but the report does **not** validate several declared or implied integrity surfaces, including:

* `core/id_registry.json` completeness against all authoritative sources
* graph edge endpoint validity in `graphs/iran_dual_layer_graph.json`
* overlay edge endpoint validity in `graphs/iran_exploit_chain_overlay.json`
* semantic dependencies declared in `schemas/module_map.json`

I manually checked the first three and they currently pass, but they are not represented in the shipped validation report.

**Why this matters:** `all_pass = true` is accurate, but narrower than the surrounding docs suggest.

---

### 4. Low — README layout under-describes the actual archive contents

`README.md:19-26` lists six families and omits the `docs/` layer, even though the archive includes a canonical explanatory doc plus archived drafts.

This is minor, but it contributes to the packaging ambiguity above.

## What is solid

These are now defensible strengths of the archive:

* the key prediction joins are real, not prose-only
* narrative clusters are first-class records
* overlay IDs are first-class records
* seeded claim IDs are authoritative and workflow-linked
* the docs layer no longer blurs present state vs future hardening in the way the older drafts did

## Bottom line

This is **not** the old broken baseline anymore. The core graph/prediction/registry structure is coherent.

The remaining problems are mainly:

1. **manifest rigor**
2. **contract exactness**
3. **validation coverage**

So I would treat this archive as:

* **usable as a strong v2 handoff**
* **not yet a fully self-sealing contract package**

## Recommended next fixes

1. Regenerate `manifest.json` so it either:

   * excludes self-hashing, or
   * uses a detached manifest/signature model

2. Add `docs/layered_stack.md` and archived docs to the manifest, or explicitly mark them out-of-scope

3. Normalize `schemas/module_map.json` exports to actual field names

4. Split `module_map` dependencies into:

   * **mechanical joins**
   * **semantic/explanatory dependencies**

5. Extend `schemas/validation_report.json` to include:

   * `id_registry` completeness
   * graph edge endpoint checks
   * overlay edge endpoint checks
   * coverage summary against every declared import in `module_map`

If you want, I can turn this into a stricter review artifact format with `High / Medium / Low` findings plus a pass/fail recommendation.

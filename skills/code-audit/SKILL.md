---
name: code-audit
description: Audit a code area for obvious, low-risk technical debt — oversized files, duplication, weak typing, missing tests, and tight coupling — and produce a prioritized, evidence-backed report. Use when the user asks to review code health, find tech debt, check for giant/hard-to-edit files, spot duplication or bad practices, or assess whether a module is accumulating debt. Not a bug hunt and not a rewrite proposal.
license: MIT
metadata:
  version: "1.0.0"
  author: "Sebastian Serna"
---

# Audit technical debt

Produce a prioritized report of **obvious, low-risk** technical debt in a target code area. The goal is to keep a young or fast-moving codebase from accumulating avoidable debt — not to over-clean it, and not to redesign its architecture.

## Scope first

Before auditing, fix the scope with the user if unclear: which directory, module, or layer. If the area is large, split the read across parallel sub-agents by concern (one for presentation/UI files, one for logic/data/server files) so no single context holds everything. Each sub-agent returns a concise, evidence-backed report — file paths and line numbers, not whole-file dumps.

Start by measuring, not reading: list the files and rank them by size (lines of code). The largest files are the first audit targets, because they are the ones that will hurt most to edit later.

## What to look for

Report findings under these buckets. For each finding give: the location (`path:line`), a one-line description, a **severity** (High / Medium / Low), and a relative **effort** (S / M / L).

1. **Oversized files / god modules.** Files that mix several unrelated responsibilities and are painful to edit. For each, sketch a *concrete* extraction: what cohesive unit (function, sub-component, helper, hook/module) to pull out and roughly which lines. Prefer extractions that are mechanical and behavior-preserving.
2. **Duplication.** The same logic or block copied across files — especially the same helper duplicated between two runtimes (e.g. one side and another that are required to stay byte-for-byte identical). Cite every location. Propose a single shared home.
3. **Weak or scattered typing.** Escape hatches that defeat the type system (untyped catch-alls, unsafe casts), the same shape redeclared inline in many places instead of one shared type, and values typed loosely where a discriminated union plus guards would let the compiler validate instead.
4. **Missing tests.** Search for existing test files in the area and map what is uncovered. Flag pure, side-effect-free logic first — it is cheap to test and high value (validators, parsers, model derivations, ordering, hashing). Then flag stateful units the project's conventions say should be tested.
5. **Coupling worth separating.** Presentation tangled with logic that belongs in a reusable unit; fragile effects (timers/watchers without cleanup, manual cancellation tokens, race conditions — especially ones already admitted in code comments); duplicated or mutated shared state.

## Boundaries — do not over-reach

- **Stay low-risk.** This audit is for debt that is cheap and safe to pay down now. Do **not** propose large rewrites or architectural changes.
- **Separate architecture from debt.** If you find a problem whose fix is a design change (a data-shape decision, an identity/versioning model, a protocol), do **not** design the solution. Flag it briefly as "architectural — out of scope" so it lands on the right roadmap or plan instead.
- **Call out non-issues.** If something looks like duplication or a god object but is actually justified (two things that model genuinely different data, an inherent recursive pattern), say so explicitly so reviewers don't waste effort "fixing" it.
- **Respect project phase.** In an early-stage area, prefer the smallest set of high-ROI fixes over exhaustive cleanup.
- **Verify before asserting.** Confirm every finding against the actual code — never claim duplication, dead code, or missing coverage without checking it. A false positive in an audit costs credibility; cite the `path:line` you looked at.

## Output

Write the report in the **language of the user's conversation**, so it reads naturally to them (the skill stays English and generic; the response adapts). Keep verbatim all code identifiers, file paths, and `path:line` citations.

A single concise report:

1. **One-paragraph verdict** — is the base healthy? Where is the debt concentrated?
2. **Findings by severity**, each with location, description, severity, effort, and (for structural items) the concrete extraction sketch.
3. **Out of scope** — architectural risks flagged but not solved, and non-issues deliberately left alone.
4. **Recommended order** — the best ROI / lowest risk sequence to tackle the findings.

If the user wants this turned into actionable work, offer to capture it as a plan or task — but keep the audit itself to findings, not implementation.

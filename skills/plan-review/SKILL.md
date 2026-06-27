---
name: plan-review
description: Review a plan/spec document — or a related set of plans — for clarity, scope discipline, correct sequencing, and internal consistency, then report concrete fixes. Use when the user asks to review a plan, validate a spec before starting it, sanity-check scope and dependencies, confirm a plan is ready to execute, or compare how several plans relate. Reviews plan documents; it does not write code or execute the plan.
license: MIT
metadata:
  version: '1.0.0'
  author: 'Sebastian Serna'
---

# Review a plan

Critically review a plan or spec document — or a related set of plans — and return concrete, actionable feedback. The aim is to catch problems while they are still cheap to fix — before the plan is executed — without rewriting the author's intent. The default unit is a single plan, reviewed in depth; when given several, review each on its own terms and then add a cross-plan pass (see "When reviewing several plans together").

## How to read it

1. Read the plan in full first. If the project defines a plan format or rules file, read that too and check the plan against it (required sections, ordering, frontmatter/metadata, naming, mandatory entry/exit phases). Do not invent rules the project doesn't have.
2. Read just enough of the referenced code or docs to verify the plan's claims — that the files it names exist, that the problem it describes is real, that a step isn't already done. A plan that misdescribes the current state is the most common and most expensive defect.

## What to evaluate

Report issues with a **severity** (Blocker / Should-fix / Nice-to-have) and a concrete suggested fix.

- **Objective clarity.** Is there a single, crisp statement of what this achieves and why? Vague or multi-objective plans drift during execution.
- **Scope discipline.** Is the scope explicit, and is anything that should be _out_ of scope named as such? Watch for scope creep, and for work that actually belongs to a different plan — flag overlap or duplication with sibling plans and suggest where it belongs.
- **Sequencing and dependencies.** Do the phases/steps run in a sound order? Are hard dependencies (something that must exist first) and ordering-by-cost (what is expensive to change once it exists) respected? Call out steps blocked by missing prerequisites.
- **Step quality.** Is each step concrete and verifiable — a single action, not a paragraph of mixed detail? Is detail in the right place (rationale and specifics in the body, not crammed into checklist items)? Is there a clear definition of done?
- **Internal consistency.** Title matches the stated goal; checklist mirrors the detailed sections; cross-references resolve; no contradictions between sections; terminology stable throughout.
- **Right-sizing.** Is the plan too big to execute as one unit (should be split), or too thin to need a plan? Is any step really an unbounded research question wearing a checkbox?
- **Risk and reversibility.** Are the risky, hard-to-reverse, or data-affecting steps identified, sequenced early or guarded, and given a fallback?
- **Actionability for the executor.** Could someone other than the author pick this up and execute it without re-deriving the context? Flag missing acceptance criteria, ambiguous ownership, or steps that can't be done by the intended executor.

## When reviewing several plans together

When given more than one plan, treat the set as a system — not just N independent reviews:

- **Dependencies and order.** Map what each plan needs from the others; flag cycles, missing prerequisites, and any sequencing that contradicts cost-of-change (the expensive-to-reverse plans should run early).
- **Overlap and duplication.** Flag work claimed by more than one plan, or work sitting in the wrong plan, and recommend where it should live.
- **Gaps and seams.** Surface work the set assumes but no plan owns, and shared interfaces or data shapes the plans must agree on.
- **Consistency across the set.** Terminology, naming, and cross-references should resolve between plans, not only within each.

## Boundaries

- **Review, don't rewrite.** Suggest concrete edits; do not silently restructure the author's plan or replace their approach with yours. If you believe the whole approach is wrong, say so plainly as a Blocker with reasoning, and let the author decide.
- **Verify before asserting.** Don't claim a step is redundant, wrong, or already done without checking the actual state. Cite what you looked at.
- **Respect the format.** If the project owns the plan template, conform to it; flag deviations rather than imposing a different structure.

## Output

Write the review in the **language of the reviewed document**, so feedback reads naturally to its author (the skill stays English and generic; the response adapts). Keep verbatim anything quoted from the document, code identifiers, file paths, and fixed template keywords the format mandates in a given language. For multiple plans in different languages, default to the shared majority language, or the user's conversation language if evenly split.

1. **Verdict** — one line: ready to execute / ready after fixes / needs rework, and why.
2. **Findings** — grouped by severity, each with the specific location in the plan and a concrete suggested fix.
3. **Open questions for the author** — genuine forks only: decisions that change the plan and that you cannot resolve from the document or the code.

Then offer to apply the agreed fixes to the plan document.

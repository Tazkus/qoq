---
name: quality-of-quantity-protocol
description: Use for backend code changes that may alter business logic, workflow shape, branching behavior, upstream/downstream relationships, or handling of multiple items. After each meaningful backend change, the main agent must explain the core business logic and use a documentation subagent, when available, to review and format the in-repository HTML summary, defaulting to docs/qoq.html. Do not proactively use for frontend-only UI, layout, styling, or component changes unless the user explicitly asks and the frontend code owns real business rules.
---

# Purpose

Maintain backend project understandability after meaningful business-logic changes.

For each applicable change, the agent must:

1. explain the latest backend business logic to the user,
2. output the core business logic structure before or alongside the documentation work, and
3. update an HTML summary document inside the project repository.

This skill exists to prevent drift between backend code, documentation, and human understanding.

# Scope

This is a backend-first skill.

Use it proactively for backend or server-side changes involving:

- backend services, controllers, APIs, jobs, queues, schedulers, workers, or command handlers
- database reads/writes, migrations, persistence rules, or transaction boundaries
- permissions, authentication, authorization, payment, settlement, inventory, pricing, discounts, or status transitions
- domain workflows, handoff rules, invocation topology, branching, fallback, retry, or partial failure
- batch handling, iteration, filtering, mapping, aggregation, data-shape conversion, or finalization

Do not proactively use it for:

- frontend-only projects
- UI display, styling, page layout, animation, visual polish, copy, or component structure
- frontend refactors that do not own business rules
- purely mechanical refactors with no behavior impact and no business-logic ambiguity
- changes where the user explicitly says not to update docs

Exception: if frontend code owns real business rules, apply this skill only when the user explicitly asks for this protocol or clearly asks to document that frontend-owned business behavior.

If a repository is full-stack, apply this skill only to the backend/server-side behavior affected by the change.

# Core principle

Follow one core principle: **quantity**.

When documenting backend business logic, explicitly account for:

- how many modules or steps exist in the full workflow
- whether the workflow is single-path or may branch
- whether a module has one fixed upstream/downstream or multiple entry points / exits
- whether a feature processes singular data only or may process multiple items

If multiplicity is possible and the logic is not fully documented, serious errors can occur. In such cases, documentation must become more detailed, not less.

# Minimum documentation protocol

For every applicable backend change, answer these questions in the HTML documentation and in the final user-facing summary.

## 1. Minimal summary

Write one short paragraph that states:

- the backend behavior that is now true
- the trigger or entry condition
- the observable result

Do not use this paragraph as a substitute for the detailed sections below.

## 2. Backend workflow decomposition

State the full backend business flow as explicit modules or steps.

Required questions:

- How many modules or steps does the complete workflow contain?
- What is the ordered sequence?
- Which steps are mandatory, and which are conditional?

Required output shape:

- Step 1: ...
- Step 2: ...
- Step 3: ...

Do not describe the flow vaguely as "processes the request" or "handles checkout". The workflow must be broken into concrete, countable stages.

## 3. Branching and exception paths

State whether the workflow is one straight path or may branch.

Required questions:

- Is this flow strictly linear?
- If it can branch, what are the branch conditions?
- Are branches mutually exclusive, cumulative, retryable, or fallback-based?
- Which branch is the default path?
- Which paths terminate early, retry, fallback, or continue to the main path?

Required documentation rule:

- Every branch must have a trigger condition.
- Every branch must have a defined result.
- Every branch must specify whether control returns to the main path or terminates early.

## 4. Upstream/downstream topology

State whether each module has a single fixed caller and callee, or multiple possible relationships.

Required questions:

- What are the entry points?
- Does this module have one fixed upstream caller, or multiple callers?
- Does this module always hand off to one fixed downstream step, or may it dispatch to multiple downstream handlers?
- Is this module reusable in multiple flows?
- Does entry context affect its behavior?

Required documentation rule:

- If a module may be invoked from multiple entry points, list them explicitly.
- If different callers cause different behavior, state the difference explicitly.
- If downstream targets differ by condition, document the routing rules.

## 5. Data and collection handling

If the feature may process multiple items, document that logic in detail.

This is mandatory for backend logic involving:

- arrays, batches, carts, order lines, item lists, candidate sets, filtered subsets, mapped results, aggregated totals, partial success, or partial failure

Document the following dimensions explicitly:

- Single item or multiple items: state which shape is accepted and what happens for each.
- Iteration: state whether logic is applied per item, after aggregation, sequentially, in parallel, or atomically.
- Filtering: state what can be excluded, why, whether exclusions are reported, and whether the remaining flow continues.
- Mapping/transformation: state whether each input maps to zero, one, or many outputs and what happens on transformation failure.
- Aggregation/finalization: state what is aggregated and whether final action uses all items, valid items, or a selected subset.
- Partial failure: state whether failures are item-level, batch-level, retryable, skipped, reported, or blocking.

The documentation must make it impossible to confuse per-item logic, per-batch logic, and aggregate-level logic.

## 6. Verification and impact scope

Document:

- which backend behavior changed
- which modules, APIs, jobs, data stores, or downstream consumers are affected
- what checks, tests, or manual scenarios verified the behavior
- what was not verified, if anything

## 7. Open questions

Only record assumptions or business questions that cannot be confirmed from code or source-of-truth docs. Do not invent business logic to fill gaps.

# Required HTML documentation behavior

For each applicable backend change, the agent must do all of the following:

1. Identify whether the change affects backend business logic.
2. Identify the smallest relevant HTML documentation target to update.
3. Update documentation so that it reflects the latest actual behavior in code.
4. Explain the updated backend business logic to the user in plain language.
5. Highlight any remaining uncertainty instead of guessing.

Documentation target rules:

- The summary document must be saved inside the project repository.
- Prefer an existing, more specific backend business HTML document when one already exists.
- Otherwise create or update `docs/qoq.html`.
- Do not use Markdown as the default summary format.
- Do not leave the summary only in the final chat response.

# Documentation subagent workflow

Use a documentation subagent for every applicable backend change when subagents are available.

Main agent responsibilities:

- Own the source-of-truth analysis of the code change.
- Produce the core backend business logic structure: minimal summary, workflow steps, branch conditions, topology, collection handling, verification scope, and open questions.
- Give the documentation subagent the relevant changed files, existing HTML documentation target, and the core business logic structure.
- Review the subagent output before finalizing and correct any mismatch with the code.
- Explain the latest backend business logic to the user in the final response.

Documentation subagent responsibilities:

- Recheck the provided core business logic structure against the relevant files and documentation.
- Identify missing workflow steps, branch conditions, topology relationships, collection rules, verification gaps, or unsupported assumptions.
- Update or draft the HTML documentation using the required sections, table rules, and diagram rules.
- Improve the HTML structure, readability, and maintainability without changing the business meaning.
- Report the documentation file path changed, any uncertainty, and any corrections it made.

Rules for using the documentation subagent:

- Do not delegate code implementation to the documentation subagent.
- Do not let the documentation subagent become the only source of business truth; the main agent remains accountable for accuracy.
- Keep the subagent task bounded to HTML documentation review, formatting, structure, readability, and coverage checks.
- If subagents are unavailable in the current environment, the main agent must perform the documentation work directly and state in the final response that the required subagent step could not be run.

# HTML chart and table rules

Use the clearest HTML structure for each section. The goal is not visual polish; the goal is fast, reliable backend comprehension. Keep examples and diagrams compact enough to maintain by hand.

Required display expectations:

- Minimal summary: use one short paragraph plus a compact metadata block when trigger, input, output, or status needs to be scanned quickly.
- Backend workflow decomposition: use an ordered list for the canonical step count. Add a flowchart when the workflow has more than five steps, loops, compensation, or multiple exits.
- Branching and exception paths: use a table with at least condition, behavior, result, and control-flow columns. A diagram may supplement the table, but must not replace the exact branch rules.
- Upstream/downstream topology: use a table for exact entry points and downstream targets. Add a topology diagram when the affected module has multiple callers, multiple downstream targets, or context-sensitive routing.
- Data and collection handling: use a matrix table for single/multiple item rules, iteration, filtering, mapping, aggregation, finalization, and partial failure.
- Verification and impact scope: use a table when more than one backend module, API, job, datastore, or downstream consumer is affected.
- Open questions: use a short list. Include only unresolved business assumptions that could affect implementation or operations.

Table rules:

- Branch tables must include: branch name, condition, behavior, result, and control flow.
- Topology tables must include: module, entry points, upstream callers, downstream targets, and context-sensitive behavior.
- Collection matrices must include: dimension, rule, failure behavior, and finalization impact.
- Verification tables must include: changed area, expected backend behavior, and verification performed.
- Use tables for exact rules. Do not encode exact business conditions only in prose or diagrams.

Diagram rules:

- Diagrams must be stored inside the HTML document, either as inline SVG or as a renderer-supported diagram block such as Mermaid.
- Prefer inline SVG when the document must open correctly without a network connection or build step.
- Use flowcharts for workflow shape, topology diagrams for module relationships, sequence diagrams for time/order-sensitive side effects, and state diagrams for status transitions.
- Every diagram must have a nearby text or table explanation with exact conditions and results.
- Do not use a diagram as the only source of truth for branch, collection, payment, settlement, inventory, or status-transition rules.
- Keep diagrams small. If a diagram needs more than about 10 nodes, split it by phase or replace part of it with a table.
- Label every branch edge with the condition that selects it.

Compact examples:

Branch table:

```html
<table>
  <thead>
    <tr>
      <th>Branch</th>
      <th>Condition</th>
      <th>Behavior</th>
      <th>Result</th>
      <th>Control flow</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Default approval</td>
      <td>Risk score is below the blocking threshold.</td>
      <td>Create the order and reserve inventory.</td>
      <td>Order becomes <code>RESERVED</code>.</td>
      <td>Continues to payment.</td>
    </tr>
  </tbody>
</table>
```

Collection matrix:

```html
<table>
  <thead>
    <tr>
      <th>Dimension</th>
      <th>Rule</th>
      <th>Failure behavior</th>
      <th>Finalization impact</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Filtering</td>
      <td>Unavailable items are excluded before pricing.</td>
      <td>Each excluded item is returned with a reason code.</td>
      <td>Only remaining valid items are charged.</td>
    </tr>
  </tbody>
</table>
```

Inline SVG flowchart:

```html
<figure aria-label="Order workflow flowchart">
  <svg viewBox="0 0 420 120" role="img" xmlns="http://www.w3.org/2000/svg">
    <defs>
      <marker id="arrow" markerWidth="10" markerHeight="7" refX="9" refY="3.5" orient="auto">
        <polygon points="0 0, 10 3.5, 0 7"></polygon>
      </marker>
    </defs>
    <rect x="10" y="35" width="100" height="50" rx="6"></rect>
    <text x="60" y="65" text-anchor="middle">Validate</text>
    <rect x="160" y="35" width="100" height="50" rx="6"></rect>
    <text x="210" y="65" text-anchor="middle">Reserve</text>
    <rect x="310" y="35" width="100" height="50" rx="6"></rect>
    <text x="360" y="65" text-anchor="middle">Charge</text>
    <path d="M110 60 H160" marker-end="url(#arrow)"></path>
    <path d="M260 60 H310" marker-end="url(#arrow)"></path>
  </svg>
  <figcaption>Default path: validate account, reserve accepted items, then charge once.</figcaption>
</figure>
```

Mermaid diagram block, only when the project renderer supports Mermaid:

```html
<pre class="mermaid">
flowchart LR
  Validate -->|eligible buyer| Reserve
  Validate -->|blocked buyer| Reject
  Reserve --> Charge
</pre>
```

Sequence or state diagrams should follow the same rule: keep the diagram compact and place the exact timeout, retry, compensation, or status-transition rules in a nearby table.

# HTML documentation template

When creating or updating `docs/qoq.html`, use this structure unless an existing project HTML document already has an equivalent structure.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>Backend Business Logic Summary</title>
</head>
<body>
  <main>
    <h1>Backend Business Logic Summary</h1>

    <section id="minimal-summary">
      <h2>Minimal Summary</h2>
      <p>Describe the current backend behavior, trigger, and result in one short paragraph.</p>
    </section>

    <section id="workflow">
      <h2>Backend Workflow Decomposition</h2>
      <ol>
        <li>Step 1: ...</li>
        <li>Step 2: ...</li>
        <li>Step 3: ...</li>
      </ol>
    </section>

    <section id="branches">
      <h2>Branching and Exception Paths</h2>
      <ul>
        <li>Default path: condition, behavior, result.</li>
        <li>Branch/fallback/retry/early termination: condition, behavior, result, and whether control returns.</li>
      </ul>
    </section>

    <section id="topology">
      <h2>Upstream and Downstream Topology</h2>
      <p>Entry points, upstream callers, dependencies, downstream targets, reuse, and context-sensitive behavior.</p>
    </section>

    <section id="data-handling">
      <h2>Data and Collection Handling</h2>
      <ul>
        <li>Single or multiple items: ...</li>
        <li>Iteration: ...</li>
        <li>Filtering: ...</li>
        <li>Mapping/transformation: ...</li>
        <li>Aggregation/finalization: ...</li>
        <li>Partial failure: ...</li>
      </ul>
    </section>

    <section id="verification">
      <h2>Verification and Impact Scope</h2>
      <p>Changed backend behavior, affected modules/APIs/jobs/data stores/consumers, and verification performed.</p>
    </section>

    <section id="open-questions">
      <h2>Open Questions</h2>
      <p>List only unconfirmed business assumptions that need human review.</p>
    </section>
  </main>
</body>
</html>
```

# Final user-facing summary requirements

Before finishing, tell the user:

1. what backend business logic is now true
2. which backend workflow steps exist now
3. whether the flow is linear or branched
4. whether the affected backend module has one or multiple entry points
5. whether the feature handles multiple items, and if so:
   - how iteration works
   - how filtering works
   - how mapping/transformation works
   - how aggregation/finalization works
6. which in-repository HTML documentation file was updated
7. what was verified and what remains uncertain
8. whether a documentation subagent handled HTML documentation review and formatting, or whether subagents were unavailable

Required phrasing standard:

- Be concrete.
- Use counts and explicit conditions.
- Avoid vague statements like "handles items", "supports branching", or "processes data".
- State exact behavior.

# Non-negotiable rules

- Do not guess business logic that is not supported by code or source-of-truth docs.
- Do not hide multiplicity behind vague wording.
- Do not collapse item-level rules into aggregate-level summaries.
- Do not use Markdown as the default project summary document.
- Do not leave the summary outside the repository.
- Do not say "same as before" when behavior became more specific.
- If a collection is involved, document iteration, filtering, mapping, and aggregation explicitly.
- If uncertainty remains, say what is uncertain and what should be reviewed by a human.

# Done criteria

This skill is complete only if all of the following are true:

- the latest backend business logic has been explained to the user
- the relevant in-repository HTML documentation has been updated
- the workflow has been decomposed into explicit steps
- branching has been documented where applicable
- upstream/downstream relationships have been documented where applicable
- multiplicity rules have been documented where applicable
- verification and impact scope are recorded
- if subagents are available, HTML documentation review and formatting were handled by a documentation subagent
- if subagents are unavailable, the final response explicitly says so and the main agent completed the HTML documentation directly
- no unsupported assumptions were added

---
name: quality-of-quantity-protocol
description: Use when code changes may alter business logic, workflow shape, branching behavior, upstream/downstream relationships, or handling of multiple items. After each meaningful change, explain the latest business logic to the user and update the relevant documentation using the minimum documentation protocol. Do not use for code changes that are purely mechanical and cannot affect behavior.
---

# Purpose

Ensure that every meaningful code change is accompanied by:

1. a clear explanation of the latest business logic to the user, and
2. a synchronized documentation update.

This skill exists to prevent business-logic drift between code, documentation, and human understanding.

# Core principle

Follow one core principle: **quantity**.

When documenting business logic, the agent must explicitly account for:

- how many modules or steps exist in the full workflow
- whether the workflow is single-path or may branch
- whether a module has one fixed upstream/downstream or multiple entry points / exits
- whether a feature processes singular data only or may process multiple items

If multiplicity is possible and the logic is not fully documented, serious errors can occur. In such cases, documentation must become more detailed, not less.

# When to use

Use this skill when a change may affect any of the following:

- business workflow steps
- branching conditions
- call paths or invocation topology
- module boundaries or handoff rules
- batch handling, iteration, filtering, aggregation, mapping, partial failure, fallback, or data-shape conversion
- payment, settlement, inventory, pricing, discount, status transition, or other business-critical logic
- API behavior or any user-visible behavior tied to process flow

Do not use this skill when:

- the change is purely cosmetic
- the change is a refactor with no behavior impact and no business-logic ambiguity
- the user explicitly says not to update docs

If uncertain whether the change affects business logic, assume that it does and apply this skill.

# Minimum documentation protocol

For every meaningful change, the agent must answer these questions in the documentation and in the final user-facing summary.

## 1. Workflow decomposition

State the full business flow as explicit modules or steps.

Required questions:

- How many modules or steps does the complete workflow contain?
- What is the ordered sequence?
- Which steps are mandatory, and which are conditional?

Required output shape:

- Step 1: ...
- Step 2: ...
- Step 3: ...

Do not describe the flow vaguely as “processes the request” or “handles checkout”.
The workflow must be broken into concrete, countable stages.

## 2. Branching behavior

State whether the workflow is one straight path or may branch.

Required questions:

- Is this flow strictly linear?
- If it can branch, what are the branch conditions?
- Are branches mutually exclusive, cumulative, retryable, or fallback-based?
- Which branch is the default path?

Required documentation rule:

- Every branch must have a trigger condition.
- Every branch must have a defined result.
- Every branch must specify whether control returns to the main path or terminates early.

## 3. Upstream/downstream topology

State whether each module has a single fixed caller and callee, or multiple possible relationships.

Required questions:

- Does this module have one fixed upstream caller, or multiple callers?
- Does this module always hand off to one fixed downstream step, or may it dispatch to multiple downstream handlers?
- Is this module reusable in multiple flows?
- Does entry context affect its behavior?

Required documentation rule:

- If a module may be invoked from multiple entry points, list them explicitly.
- If different callers cause different behavior, state the difference explicitly.
- If downstream targets differ by condition, document the routing rules.

## 4. Multiplicity and collection handling

If the feature may process multiple items, the agent must document that logic in detail.

This is mandatory for any business logic involving:

- arrays
- batches
- carts
- order lines
- item lists
- candidate sets
- filtered subsets
- mapped results
- aggregated totals
- partial success / partial failure

The agent must explicitly document at least the following four dimensions.

### 4.1 Iteration

Required questions:

- Is the logic applied once per item, or once after aggregation?
- Does user confirmation happen per item or once for the whole batch?
- Is failure handled item-by-item or at the overall transaction level?
- Is execution sequential, parallel, or logically atomic?

Example concern:
In a cart checkout flow, does each item require a separate payment confirmation, or is the total settled once after aggregation?

### 4.2 Filtering

Required questions:

- Can some items be excluded during processing?
- What conditions cause exclusion?
- Are excluded items silently dropped, explicitly reported, retried later, or converted into warnings?
- If some items are filtered out, does the remaining flow continue normally?
- Does billing, settlement, or state transition still proceed for the remaining valid items?

Example concern:
In a cart checkout flow, can an out-of-stock item be filtered out while the rest of the order still proceeds to charge?

### 4.3 Mapping and transformation

Required questions:

- Does each input item map to exactly one output, zero outputs, or multiple outputs?
- What happens when transformation fails for one item?
- Can invalid transformed data corrupt the whole flow?
- Are malformed outputs rejected, defaulted, skipped, retried, or escalated?

Example concern:
If discount application fails for one product, can it produce invalid pricing data that breaks the entire checkout flow?

### 4.4 Aggregation and finalization

Required questions:

- Are results aggregated before final action?
- What exactly is aggregated: price, quantity, availability, status, risk, or permissions?
- Is the final action based on all items, only valid items, or a selected subset?
- Can partial results still trigger finalization?

The documentation must make it impossible to confuse:

- per-item logic
- per-batch logic
- aggregate-level logic

# Required documentation behavior

For each change, the agent must do all of the following:

1. Identify whether the change affects business logic.
2. Identify the smallest relevant documentation target to update, preferring existing docs over creating new files.
3. Update documentation so that it reflects the latest actual behavior in code.
4. Explain the updated business logic to the user in plain language.
5. Highlight any remaining uncertainty instead of guessing.

Preferred documentation targets:

1. existing README sections
2. existing docs pages under docs/
3. architecture or workflow docs
4. inline docstrings or code comments near the affected module
5. changelog or migration notes, if the repository already uses them

Do not create a new document unless no suitable existing location exists.

# Documentation template

When updating docs, use this template whenever applicable.

## Business logic summary

- Goal:
- Trigger:
- Inputs:
- Outputs:

## Workflow steps

1. ...
2. ...
3. ...

## Branches

- Branch A:
    - Condition:
    - Behavior:
    - Result:
- Branch B:
    - Condition:
    - Behavior:
    - Result:

## Module topology

- Entry points:
- Upstream dependencies:
- Downstream targets:
- Reusable in other flows:
- Context-sensitive behavior:

## Multiplicity rules

- Processes single item or multiple items:
- Iteration rule:
- Filtering rule:
- Mapping rule:
- Aggregation rule:
- Partial failure rule:
- Finalization rule:

## Edge cases and invariants

- Must never happen:
- Allowed partial outcomes:
- Guaranteed postconditions:

# Final user-facing summary requirements

Before finishing, the agent must tell the user:

1. what business logic is now true
2. which workflow steps exist now
3. whether the flow is linear or branched
4. whether the affected module has one or multiple entry points
5. whether the feature handles multiple items, and if so:
    - how iteration works
    - how filtering works
    - how mapping/transformation works
    - how aggregation/finalization works
6. which documentation files were updated

Required phrasing standard:

- Be concrete.
- Use counts and explicit conditions.
- Avoid vague statements like “handles items”, “supports branching”, or “processes data”.
- State exact behavior.

# Non-negotiable rules

- Do not guess business logic that is not supported by code or existing source-of-truth docs.
- Do not hide multiplicity behind vague wording.
- Do not collapse item-level rules into aggregate-level summaries.
- Do not say “same as before” when behavior became more specific.
- If a collection is involved, document iteration, filtering, mapping, and aggregation explicitly.
- If uncertainty remains, say what is uncertain and what should be reviewed by a human.

# Done criteria

This skill is complete only if all of the following are true:

- the latest business logic has been explained to the user
- the relevant documentation has been updated
- the workflow has been decomposed into explicit steps
- branching has been documented where applicable
- upstream/downstream relationships have been documented where applicable
- multiplicity rules have been documented where applicable
- no unsupported assumptions were added

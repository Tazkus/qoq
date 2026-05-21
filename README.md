# quality-of-quantity-protocol

A coding agent skill that preserves backend project understandability after business-logic changes.

## What this skill does

When a backend code change may affect business behavior, this skill requires the agent to:

1. explain the latest business logic in concrete terms;
2. keep the main agent responsible for the core business-logic structure;
3. use a documentation subagent, when available, to review and format the in-repository HTML summary;
4. explicitly document workflow steps, branching, module topology, collection handling, verification, and open questions.

The default documentation target is `docs/qoq.html`. If a project already has a more specific backend business HTML document, the skill prefers updating that existing document.

This is a backend-first skill. It does not proactively apply to frontend-only UI, styling, layout, or component changes unless the user explicitly asks and the frontend code owns real business rules.

## Included skill file

- `SKILL.md`: full behavior contract for this skill.

## Human-facing examples

- `assets/showcase/qoq-showcase.html`: optional HTML showcase for maintainers. It is intentionally stored under `assets/` and is not referenced by `SKILL.md`, so normal skill invocation does not need to load it.

## Install in OpenAI Codex

Paste this repository URL in Codex. If your client supports one-click plugin/skill install prompts, it should offer an install action automatically.

If needed, you can also ask Codex directly:

```text
Fetch and follow instructions from https://raw.githubusercontent.com/Tazkus/qoq/refs/heads/main/.codex/INSTALL.md
```

Then start a new session and ask Codex to perform a backend business-logic-affecting code change to verify the skill is loaded.

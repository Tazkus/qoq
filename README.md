# quality-of-quantity-protocol

A coding agent skill that enforces a minimum documentation protocol for business-logic changes.

## What this skill does

When a code change may affect business behavior, this skill requires the agent to:

1. explain the latest business logic in concrete terms;
2. update relevant docs so code and docs stay aligned;
3. explicitly document workflow steps, branching, module topology, and multiplicity rules.

## Included skill file

- `SKILL.md`: full behavior contract for this skill.

## Install in OpenAI Codex

Paste this repository URL in Codex. If your client supports one-click plugin/skill install prompts, it should offer an install action automatically.

If needed, you can also ask Codex directly:

```text
Fetch and follow instructions from https://raw.githubusercontent.com/Tazkus/qoq/refs/heads/main/.codex/INSTALL.md
```

Then start a new session and ask Codex to perform a business-logic-affecting code change to verify the skill is loaded.

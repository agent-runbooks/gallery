# AGENTS.md

Each runbook lives in `skills/<name>/`. What a pull request with a runbook needs: the Contributing section of `README.md`.

## Adding a runbook

A new runbook lands in three places in one change:

1. `skills/<name>/`.
2. A row in the `## Runbooks` table of `README.md`.
3. An entry in `plugins` of `.claude-plugin/marketplace.json`.

## Dependencies

Runbook code uses the standard library of Python 3.10 only. What a run needs from outside, MCP servers, harnesses, models, is listed in the Requires column of the README table and in the runbook's own README.

## Engine

`runbook.py` in a runbook is a copy of the engine release its `__version__` names, and CI compares it with that tag. To move a runbook to a newer engine, follow the upgrade paragraph of `skills/agent-runbook-authoring/SKILL.md` in [agent-runbooks/skills](https://github.com/agent-runbooks/skills), and carry the release's sections from `references/template.md`: Execution rules into the runbook's `SKILL.md`, Executor constraints into `prompts/common.md`.

## Checks

Run the step of `.github/workflows/test.yml` locally before calling a change done.

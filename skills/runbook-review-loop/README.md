# runbook-review-loop

A [runbook](https://github.com/agent-runbooks/skills) for one code change in a git repository. A coder implements the brief, a reviewer reads the changes and runs the checks, and the coder fixes the findings until none are left. When the fix rounds run out with findings open, the human decides: more rounds or stop. The changes stay uncommitted, with a review file that says what they do.

Every step is a subagent of the session that runs the runbook, the Agent tool in Claude Code. Nothing to install beyond the skill: no MCP server, no other harness.

```
SKILL.md           what the orchestrator reads: inputs, execution rules, end of run
flow.py            steps, the flow, the two executors
agent_runbooks.py  the engine, a copy of agent-runbook-authoring's
prompts/           common.md and one prompt per step
```

## Try it

Install the skill with the skills CLI into the user directory; it asks which agents to install it on:

```bash
npx skills add agent-runbooks/gallery --skill runbook-review-loop -g
```

Or, in Claude Code, as a plugin:

```bash
claude plugin marketplace add agent-runbooks/gallery
claude plugin install runbook-review-loop@agent-runbooks-gallery
```

Other agents and ways to install: [the repository README](../../README.md#install). Then start a session in a git repository with a clean tree and ask your agent, with your own change and checks:

> Run runbook-review-loop: add an optional `max_length` to `slugify`, cut on a word boundary. The checks are `python3 -m unittest`.

Without checks in the request, the executors run the ones the repository's `AGENTS.md` or `CLAUDE.md` names. The run directory is `.agent-runbooks/runs/<date>-<slug>/` under the directory the session started in. To keep it out of version control, add `.agent-runbooks/runs/` to `.gitignore` and commit that before the run: an uncommitted edit is part of the changes the reviewer reads.

## Inputs

- `brief`: the change, in words.
- `repo`: the repository to change, the directory the session started in unless you name another.
- `checks`: the command that checks the project. Optional.
- `maxFixRounds`: fix rounds before the human is asked, 2 by default.

## Change it

Both executors run on the model of the main session. To put the reviewer on another model, or for other steps or another flow, copy the skill under another name, the folder and the `name` in its `SKILL.md`, and edit `flow.py` and the prompts of the copy: an update of the installed skill overwrites edits made in place. [agent-runbook-authoring](https://github.com/agent-runbooks/skills/tree/main/skills/agent-runbook-authoring) helps with that.

A second reviewer on another vendor's model, triage of the findings and a per-repository profile are in [runbook-implement-task](../runbook-implement-task). It runs its steps through throng-mcp.

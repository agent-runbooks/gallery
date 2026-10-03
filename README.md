# Agent Runbooks gallery

Runbooks to install and adapt. A runbook is a procedure an agent session runs step by step through subagents. The skill that writes runbooks and the viewer that shows a run live in [agent-runbooks/skills](https://github.com/agent-runbooks/skills).

A runbook is a skill in the [Agent Skills](https://agentskills.io) format, so it installs like any other skill and carries its own copy of the engine. Running one does not need the authoring skill.

## Install

Pick one way, otherwise each runbook shows up twice.

<details>
<summary><strong>Claude Code plugin</strong></summary>

```bash
claude plugin marketplace add agent-runbooks/gallery
claude plugin install runbook-task-cycle@agent-runbooks-gallery
```

</details>

<details>
<summary><strong>skills CLI: Claude Code, Codex, opencode, Cursor and others</strong></summary>

```bash
npx skills add agent-runbooks/gallery
```

It asks which runbooks to take and which agents to install them on. One runbook into the user directory of one agent:

```bash
npx skills add agent-runbooks/gallery --skill runbook-task-cycle -g -a claude-code -y
```

</details>

<details>
<summary><strong>By hand</strong></summary>

Copy `skills/<name>` into your harness's skills directory: `~/.claude/skills`, `~/.codex/skills`, `~/.config/opencode/skills`.

</details>

## Runbooks

### ◆ runbook-task-cycle

One coding task end to end: a coder implements the brief, a cheap model runs the checks, two models from different vendors review independently, an arbiter triages their findings, the coder fixes, a verifier checks the fixes, a last pass cleans up comments and wording. A project adapts it with one profile file: its checks, its version control, its rules, its models. Needs [throng](https://github.com/agent-runbooks/throng-mcp): every step runs as a thronglet. [Read more](skills/runbook-task-cycle).

## Contributing

**An idea.** Open an issue with the `idea` label. Say what the runbook does, what the human gives it and what it leaves behind. An accepted idea is not a promise that someone builds it.

**A runbook.** Open a pull request that adds `skills/<name>`. [agent-runbook-authoring](https://github.com/agent-runbooks/skills/tree/main/skills/agent-runbook-authoring) writes one with you. The pull request needs:

- a README with what the runbook requires (MCP servers, tools, models), the install command and an example request
- `python flow.py --check` passing
- `runbook.py` equal byte for byte to the engine release its `__version__` names, so a reviewer reads your steps and prompts and not the engine again
- one real run described in the pull request: the request, the path the run took, what it produced

CI checks the second and the third.

## License

MIT

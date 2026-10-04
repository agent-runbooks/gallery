# Agent Runbooks gallery

Ready-made runbooks for AI agents. Install a procedure, run it in your session, and adapt it to your work.

A runbook is an [Agent Skill](https://agentskills.io) that an agent session runs step by step through subagents. How it works, and how to write your own: [agent-runbooks/skills](https://github.com/agent-runbooks/skills).

## Runbooks

| Runbook | What you get | Requires |
|---|---|---|
| [runbook-task-cycle](skills/runbook-task-cycle) | one coding task implemented and reviewed, with changes left uncommitted | [throng-mcp](https://github.com/agent-runbooks/throng-mcp) |

## Install

You need Python 3.10 or newer, a harness whose session can launch subagents and learn when they finish, and what the runbook lists under Requires. Pick one way to install, otherwise each runbook shows up twice.

<details>
<summary><strong>Claude Code plugin</strong></summary>

One plugin per runbook:

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

To see a run's status in the chat after every step, install [runbook-viewer](https://github.com/agent-runbooks/skills/tree/main/skills/runbook-viewer) as well.

## Adapt a runbook

1. **Settings first.** Each runbook's README says what you can change without touching its steps. runbook-task-cycle, for one, takes a [profile file](skills/runbook-task-cycle#the-profile) per repository: its checks, version control, rules and models.
2. **Then a copy.** When you need other steps, copy the runbook into your skills directory under another name and edit `flow.py` and the prompts. [agent-runbook-authoring](https://github.com/agent-runbooks/skills/tree/main/skills/agent-runbook-authoring) helps with that, and with a new runbook from scratch.

## Contributing

**An idea.** Open an issue with the `idea` label. Say what the runbook does, what the human gives it and what it leaves behind. An accepted idea is not a promise that someone builds it.

**A runbook.** Open a pull request that adds `skills/<name>`. [agent-runbook-authoring](https://github.com/agent-runbooks/skills/tree/main/skills/agent-runbook-authoring) writes one with you. The pull request needs:

- a README with what the runbook requires (MCP servers, tools, models), the install command and an example request
- `python flow.py --check` passing
- `runbook.py` equal byte for byte to the engine release its `__version__` names, so a reviewer reads your steps and prompts and not the engine again
- one real run described in the pull request: the request, the path the run took, what it produced

CI checks the second and the third.

## License

[MIT](LICENSE)

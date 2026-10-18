# runbook-implement-task

A [runbook](https://github.com/agent-runbooks/skills) for one coding task in a repository, from brief to reviewed, uncommitted changes. A coder implements the brief, the checks run as plain code, two different models review independently, an arbiter triages their findings against the code, the coder fixes what is worth fixing, a verifier checks the fixes, and a last pass cleans up comments and wording. The human is asked only when nothing names the checks, when the repository is dirty at the start, or when the fix rounds run out. Commit, PR and CI stay outside.

Every step that needs a model runs through [throng-mcp](https://github.com/agent-runbooks/throng-mcp), an MCP server that runs Claude Code, Codex or OpenCode as a subagent of any of them and returns the agent's final message, which is the shape a runbook step already has. The session that runs this runbook needs it.

```
SKILL.md           what the orchestrator reads: inputs, execution rules, end of run
flow.py            steps, the flow, the code of preflight and checks, the default agent of each executor
agent_runbooks.py  the engine, a copy of agent-runbook-authoring's
prompts/           common.md and one prompt per agent step
```

## Models

Three executors, each an agent string as throng-mcp names it, `<harness>/<model>[:<effort>]`:

| Executor | Steps | Default |
|---|---|---|
| `top` | triage, review B when `second` fails or cannot run | `claude/fable:high` |
| `strong` | implement, fix-checks, fix, polish, review A, verify | `claude/opus:high` |
| `second` | review B | `codex/gpt-6.1-sol:high` |

Each is an input of the run, so a project changes them in its profile and the human changes them for one run in words: "run implement-task with astra as the second reviewer".

The default `second` is Codex, and it is optional: without it review B runs on `top`, and with the default executors both reviews come from one vendor.

Preflight and checks need no model: `flow.py` runs the `statusCommand` and `checks` commands itself and records their exit codes.

## Try it

Install the skill with the skills CLI into the user directory; it asks which agents to install it on:

```bash
npx skills add agent-runbooks/gallery --skill runbook-implement-task -g
```

Or, in Claude Code, as a plugin:

```bash
claude plugin marketplace add agent-runbooks/gallery
claude plugin install runbook-implement-task@agent-runbooks-gallery
```

Other agents and ways to install: [the repository README](../../README.md#install). The session also needs [throng-mcp](https://github.com/agent-runbooks/throng-mcp) connected. Then start a session in a repository and ask your agent, with your own task and checks:

> Run runbook-implement-task: add an optional `max_length` to `slugify`, cut on a word boundary. The checks are `python3 -m unittest`.

## The profile

A project adapts the runbook with one file, `<repo>/.agent-runbooks/implement-task.md`, committed. The orchestrator copies it into the run directory, every executor reads it, and each of its sections replaces a default of the runbook. All sections are optional; without the file the defaults hold: git, the checks command the human gives, the repository's instructions file. Keep `.agent-runbooks/runs/` out of version control, not the whole directory.

```markdown
# Implement task

## Checks

command: pnpm check

Typecheck, lint and the unit tests, each even if an earlier one failed. It takes a minute; the e2e suite is not part of it.

## VCS

Git. Everything uncommitted is the changes, except `pnpm-lock.yaml`: the install rewrites it, and nobody reviews it.

## Rules

- Comments, messages and documentation in English.
- `@ts-expect-error` is a finding, whatever the reason next to it.
- Generated files under `__generated__/` are regenerated with `pnpm codegen`, never edited.

## Executors

- second: codex/gpt-6-astra:high
- strong: claude/opus:max
```

What each section is for:

- **Checks**: a line `command: <shell command>`, which goes to `start` as the `checks` input, so the human omits it. `flow.py` runs it from the repository root, and the checks pass when it exits 0. Below it, what the executors should know about the checks. Checks that depend on which files changed, such as a monorepo that runs the checks of each package the changes touch, go into a script the command runs.
- **VCS**: for a repository under something other than git, a line `status: <shell command>` that lists the uncommitted files, one per line and nothing when the tree is clean, which goes to `start` as the `statusCommand` input; and how to show the changes, for the executors.
- **Rules**: the project rules that matter in a task, for the coder and the reviewers' third axis. A digest, since the executors read the instructions file anyway: the rules that are broken often, or that the file states too far down to be noticed.
- **Executors**: the agent of the executors to change, one per line.

The profile is prose read by models, so anything the steps need to know about the project goes there in the words you would use for a new colleague. When the profile is not enough, because the project needs another step or another graph, copy the skill into the project's skills directory under another name and edit `flow.py` and the prompts. From then on it is yours: the engine upgrade procedure is in [agent-runbook-authoring](https://github.com/agent-runbooks/skills/blob/main/skills/agent-runbook-authoring/SKILL.md).

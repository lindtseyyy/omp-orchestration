# omp orchestration

A `/orchestrate` slash command for [omp](https://github.com/can1357/oh-my-pi). Your main session becomes the orchestrator; it delegates to three subagents:

| Agent | Role | Model (default) |
|---|---|---|
| main session | orchestrator: plan → phases → relay questions → verify-fix loop (max 3 rounds) | whatever the session runs (use Opus 5.5 high) |
| `planner` | read-only research and plan with per-step file lists and success checks | Opus 5.5 `high` |
| `coder` | implements within assigned files, follows the `ponytail` skill | GPT-6.1-Sol `xhigh` |
| `designer` | UI/UX within assigned files | Gemini 3.1 Pro `high` |

Workers never talk to each other; questions go to the orchestrator (`agent://Main`), which routes them to the right agent.

Based on [burkeholland's Ultralight Orchestration](https://gist.github.com/burkeholland/0e68481f96e94bbb98134fa6efd00436).

## Install

From the repo:

```bash
git clone https://github.com/lindtseyyy/omp-orchestration
cd omp-orchestration
mkdir -p ~/.omp/agent/agents ~/.omp/agent/commands
cp agents/*.md ~/.omp/agent/agents/
cp commands/*.md ~/.omp/agent/commands/
```

Or from the gist (no clone):

```bash
G=https://gist.githubusercontent.com/lindtseyyy/0c1a637f72607c903a1863d55aec90b9/raw
mkdir -p ~/.omp/agent/agents ~/.omp/agent/commands
for a in planner coder designer; do curl -fsSL "$G/$a.agent.md" -o ~/.omp/agent/agents/$a.md; done
curl -fsSL "$G/orchestrate.command.md" -o ~/.omp/agent/commands/orchestrate.md
```

Then:

1. Merge `config.example.yml` into `~/.omp/agent/config.yml` (model per agent; the `:high`/`:xhigh` suffix is effort). Use models you are logged in to (`omp models`).
2. Optional: install the [ponytail](https://github.com/DietrichGebert/ponytail) skill for the coder; without it the coder ignores that line.
3. Recommended: `omp config set magicKeywords.orchestrate false`. omp's built-in `orchestrate` keyword injects a conflicting contract whenever the word appears in a prompt.
4. Restart omp (or `/reload-plugins`).

## Use

Put the main session on a strong model (`/model`), then:

```
/orchestrate add dark mode to the app
```

Prompts without `/orchestrate` run normally.

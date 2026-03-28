# Agent Operating Guide (Repo)

This file is for coding agents working in this repository.

## Repo Orientation

workspace: /home/hydrotoxin/Projects/ai/agent-swarm/workspace

## Paths (all agents)

**Workspace root** is the path on the `workspace:` line above. Call it **`WORKSPACE`**.

**Project slug** **`PROJECT`** (e.g. `weather-ai`) is the directory name under **`WORKSPACE`**.

**`APP_ROOT = $WORKSPACE/$PROJECT`** — the **product** repository (app source, `package.json`, tests).

**`SWARM_ROOT = $WORKSPACE/$PROJECT/.agent-swarm`** — **only** pipeline/spec/task/docs/evidence for the AI system. Keeps swarm artifacts separate from product folders (`app/`, `src/`, etc.).

All **specs, tasks, docs, and evidence** for the swarm live under **`SWARM_ROOT`**, not loose under **`APP_ROOT`**. File names are **short** (no `weather-ai-` prefix); **`PROJECT`** and **`.agent-swarm`** provide scope.

| Role | Path |
|------|------|
| Setup spec (**SPEC**) | `$WORKSPACE/$PROJECT/.agent-swarm/specs/setup.md` |
| Task list | `$WORKSPACE/$PROJECT/.agent-swarm/tasks/tasklist.md` |
| Pipeline log | `$WORKSPACE/$PROJECT/.agent-swarm/docs/pipeline-log.md` |
| Frontend architecture | `$WORKSPACE/$PROJECT/.agent-swarm/docs/frontend-architecture.md` |
| Backend architecture | `$WORKSPACE/$PROJECT/.agent-swarm/docs/backend-architecture.md` |
| Agent memory | `$WORKSPACE/$PROJECT/.agent-swarm/docs/agent-memory.md` |
| Evidence (e.g. reality check) | `$WORKSPACE/$PROJECT/.agent-swarm/evidence/` |

**Application source** (Next.js `app/`, `src/`, product `package.json`, etc.) lives under **`APP_ROOT`** only — not inside **`.agent-swarm`** unless a task explicitly updates swarm files.

- **Do not** rely on the shell’s current working directory. Use **absolute** paths, or `cd "$APP_ROOT"` / `cd "$WORKSPACE/$PROJECT/.agent-swarm"` in the **same** bash command before relative paths.
- In **delegation prompts**, pass **full** paths so subagents do not resolve from the wrong cwd.

Resolve `WORKSPACE` once per turn from this file if needed, e.g.:

```bash
WORKSPACE=$(sed -n 's/^workspace:[[:space:]]*//p' AGENTS.md | head -1)
```

## Migration (old layout → `.agent-swarm`)

If you still have **workspace-level** folders or specs **directly under `APP_ROOT`** (without **`.agent-swarm`**), consolidate under **`$WORKSPACE/$PROJECT/.agent-swarm/`**:

| Old | New |
|-----|-----|
| `project-specs/<PROJECT>-setup.md` | `<PROJECT>/.agent-swarm/specs/setup.md` |
| `project-tasks/<PROJECT>-tasklist.md` | `<PROJECT>/.agent-swarm/tasks/tasklist.md` |
| `project-docs/<PROJECT>-pipeline-log.md` | `<PROJECT>/.agent-swarm/docs/pipeline-log.md` |
| `project-docs/<PROJECT>-architecture.md` (split by concern) | `<PROJECT>/.agent-swarm/docs/frontend-architecture.md` and/or `backend-architecture.md` |
| Old single `architecture.md` / `ux.md` at swarm `docs/` | `frontend-architecture.md` + `backend-architecture.md` |
| `project-docs/<PROJECT>-agent-memory.md` | `<PROJECT>/.agent-swarm/docs/agent-memory.md` |
| `project-evidence/<PROJECT>/...` | `<PROJECT>/.agent-swarm/evidence/...` |
| `<PROJECT>/specs/...` (at APP_ROOT, pre–agent-swarm) | `<PROJECT>/.agent-swarm/specs/...` |
| `<PROJECT>/tasks/...` | `<PROJECT>/.agent-swarm/tasks/...` |
| `<PROJECT>/docs/...` (pipeline/agent docs only) | `<PROJECT>/.agent-swarm/docs/...` |
| `<PROJECT>/evidence/...` | `<PROJECT>/.agent-swarm/evidence/...` |

Create missing directories with `mkdir -p "$WORKSPACE/$PROJECT/.agent-swarm/{specs,tasks,docs,evidence}"` as needed.

## Persistent agent memory (required)

Persona “memory” in agent definitions is **not** automatic. Across sessions, the durable log is:

**`$WORKSPACE/$PROJECT/.agent-swarm/docs/agent-memory.md`**

- After meaningful work, **append** (do not replace the whole file): ISO timestamp, agent name, 2–6 bullets.
- If the file does not exist, create it with `# Agent memory — <PROJECT>` then append.
- Agents that must not touch product code may still **append** to this file; they must not edit `app/` / `src/` / implementation trees unless their role allows it.

*(Previously: `project-docs/<PROJECT>-agent-memory.md` or `<PROJECT>/docs/agent-memory.md` — same purpose, new location under **`.agent-swarm`**.)*

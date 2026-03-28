# Agents Orchestrator — workflow

Visual reference for the pipeline in [`.opencode/agents/agents-orchestrator.md`](../../agents/agents-orchestrator.md).

## Flowchart

```mermaid
flowchart TD
    Start([Start: SPEC from launch]) --> P0["Phase 0: Read setup at PROJECT/.agent-swarm/specs/setup.md"]
    P0 --> Extract["Extract PROJECT from Project root, stack, description, architecture sections"]
    Extract --> Match{"SPEC at WORKSPACE/PROJECT/.agent-swarm/specs/setup.md?"}
    Match -->|No| UIR(["USER_INPUT_REQUIRED: resolve mismatch"])
    Match -->|Yes| P1["Phase 1: Delegate Senior Project Manager → PROJECT/.agent-swarm/tasks/tasklist.md"]
    P1 --> P2a["Phase 2a: Delegate UX Architect → frontend-architecture.md"]
    P2a --> P2b["Phase 2b: Delegate Backend Architect → backend-architecture.md"]
    P2b --> Loop{"Unchecked tasks remain in tasklist?"}
    Loop -->|No| P4["Phase 4: Delegate Reality Checker → PROJECT/.agent-swarm/evidence/reality-check"]
    Loop -->|Yes| Pick["Pick one specialist for current task"]
    Pick --> Impl["Delegate implementation under APP_ROOT"]
    Impl --> Rev["Delegate Code Reviewer"]
    Rev -->|PASS| Mark["Mark task complete; next task"]
    Mark --> Loop
    Rev -->|FAIL| Retry{"Retry count under 3?"}
    Retry -->|Yes| Impl
    Retry -->|No| Block(["BLOCKED → APPROVAL_REQUIRED"])
    P4 --> Done(["Final assessment; PIPELINE_COMPLETE when criteria met"])
```

While the pipeline runs, maintain an append-only log at **`PROJECT/.agent-swarm/docs/pipeline-log.md`** (see agent definition and **`AGENTS.md`**).

## Phase summary

| Phase | Purpose |
|-------|---------|
| 0 | Read spec at **`WORKSPACE/PROJECT/.agent-swarm/specs/setup.md`**; derive **PROJECT**; align with **Project root** |
| 1 | Task list from spec (**Senior Project Manager**) → **`PROJECT/.agent-swarm/tasks/tasklist.md`** |
| 2 | **UX Architect** → **`PROJECT/.agent-swarm/docs/frontend-architecture.md`**; **Backend Architect** → **`backend-architecture.md`** |
| 3 | Implement → Review loop per task; retries; optional **BLOCKED** gate |
| 4 | Integration / reality check (**Reality Checker**); evidence under **`PROJECT/.agent-swarm/evidence/`** |

**Note:** **`PROJECT`** is the slug (directory under **WORKSPACE**). Swarm artifacts live under **`WORKSPACE/PROJECT/.agent-swarm/`** — short filenames (`setup.md`, `tasklist.md`, …), no slug prefix in the file name. Product code stays under **`APP_ROOT`** (`WORKSPACE/PROJECT`).

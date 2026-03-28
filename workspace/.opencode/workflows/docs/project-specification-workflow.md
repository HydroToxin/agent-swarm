# Project Specification Agent — workflow

Visual reference for the workflow defined in [`.opencode/agents/project-specification.md`](../../agents/project-specification.md).

## Entry routing and resume shortcut

```mermaid
flowchart TD
    subgraph PathA [Path A — full structured spec in first message]
        A1[Derive PROJECT from Project root] --> A2{project-specs/PROJECT-setup.md exists?}
        A2 -->|Yes| L[Load spec]
        A2 -->|No| W[Write spec from message]
        W --> L
    end

    subgraph PathB [Path B — small talk or no structured spec]
        B1{Project identifiable?} -->|Yes| B2[Resolve PROJECT; look for setup spec]
        B1 -->|No| B3[Ask for project name or Project root]
        B3 --> B2
        B2 --> B4{Spec found?}
        B4 -->|No| B5[Prompt: no spec — template or precise slug]
        B4 -->|Yes| L
    end

    L --> TL{project-tasks/PROJECT-tasklist.md exists?}
    TL -->|Yes| RS[Resume: skip Steps 2–3; task list means SPM ran]
    TL -->|No| S234[Steps 2 → 3 → 4]
    RS --> Q4[Ask: continue implementation?]
    Q4 --> AO[Orchestrator: spec path + continue request + tasklist + pipeline log paths]
    S234 --> S2[Step 2: adjustments …]
```

## Main flowchart (Steps 2–4 when no resume)

```mermaid
flowchart TD
    Start([Start: user project request]) --> Resolve["Resolve PROJECT from Project root when possible"]
    Resolve --> Ensure["Ensure project-specs/ exists"]
    Ensure --> Check{"Spec file project-specs/PROJECT-setup.md exists?"}

    Check -->|Yes| Read["Read full spec; keep in context"]
    Check -->|No| HasTemplate{"First message has Project, Project root, Tech stack, Description?"}

    HasTemplate -->|Yes| Create["Normalize and write setup spec"]
    HasTemplate -->|No| AskTemplate["Send required layout and example; wait for user"]
    AskTemplate --> Create

    Read --> Step2
    Create --> Step2

    Step2["Step 2: make changes to spec?"] --> Adj{"Yes?"}
    Adj -->|Yes| Edit["User describes changes; update file and save"]
    Adj -->|No| Step3
    Edit --> Step3

    Step3["Step 3: Software Architect review?"] --> Arch{"Yes?"}
    Arch -->|Yes| SA["Delegate Software Architect\nevaluate request + architecture"]
    Arch -->|No| Step4
    SA --> Step4

    Step4["Step 4: start implementation?"] --> Impl{"Yes?"}
    Impl -->|Yes| AO["Delegate Agents Orchestrator\nread spec + implementation pipeline"]
    Impl -->|No| EndNo([End: implementation not started])
    AO --> EndYes([End: pipeline delegated])
```

## Step summary

| Step | Purpose |
|------|---------|
| Entry | Path A (full spec first message) or Path B (small talk → identify project → find or create spec) |
| Resume | If `project-tasks/{{PROJECT}}-tasklist.md` exists → skip Steps 2–3; ask only to **continue implementation** (then Orchestrator) |
| 1 | Mechanics: resolve `{{PROJECT}}`, ensure `project-specs/`, load or create `project-specs/{{PROJECT}}-setup.md` |
| 2 | Optional edits to the setup spec |
| 3 | Optional Software Architect pass |
| 4 | Start or **continue** implementation → Agents Orchestrator |

**Note:** Product code is never implemented by this agent; only the setup spec document and delegations as described.

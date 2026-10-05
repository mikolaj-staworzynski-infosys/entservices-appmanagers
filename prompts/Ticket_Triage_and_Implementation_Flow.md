# Ticket Triage and Implementation Flow

This presentation view follows the two prompts: triage first, then implementation from the handoff. The prompt files remain the source of the detailed instructions and stop conditions.

## 1. Ticket Triage

```mermaid
flowchart TD
    A([Jira ticket and optional logs]) --> B[Step 1: Read ticket and logs]
    B --> C[Step 2: Identify first failure and component clues]
    C --> D[Step 3: Map clues to repository ownership]
    D --> E[Step 4: Decide change type and confidence]
    E --> F[Step 5: Check repo count, questions, order, and backups]
    F --> G{Code change needed?}
    G -->|No: not a code change or zero confirmed repos| H[Handoff: zero repos and explain why]
    G -->|Yes| I[Handoff: repos, changes, build, order, and evidence]
    H --> J([Triage complete])
    I --> J

    P[Send a brief progress update before each triage step] -.-> B
```

## 2. Code Change From Handoff

```mermaid
flowchart TD
    A([Handoff and optional logs]) --> B{Any confirmed repos to change?}
    B -->|No| Z[Report no code change; do not create branches]
    B -->|Yes| C[Step 0A: Preflight every repo]
    C --> D{All worktrees clean, develop branches available, topic branches absent?}
    D -->|No| X[STOP: report blocker before creating any branch]
    D -->|Yes| E[Step 0B: Update develop in every repo]
    E --> F{All develop updates succeed?}
    F -->|No| X
    F -->|Yes| G[Step 0C: Create the same topic branch in every repo]
    G --> H[For each repo, follow handoff order]
    H --> I[Step 1: Read relevant repo docs and identify owning component]
    I --> J{Step 2: Does this repo need a change?}
    J -->|No| K[Record NO CHANGE NEEDED; skip remaining repo steps]
    K --> Y{More repos in handoff?}
    J -->|Unlisted repo also appears necessary| X
    J -->|Yes| L[Step 3: Trace logs and establish timeline]
    L --> M[Step 4: Trace and document root cause]
    M --> N{Confirmed change type?}
    N -->|Not a code change| O[Do not edit; explain and report]
    N -->|New feature| Q[Step 4B: Prepare plan; make no source edits]
    Q --> R([STOP and wait for approval])
    R --> S{Plan approved?}
    S -->|No or pending| T[Remain stopped; report waiting status]
    S -->|Yes| U[Continue with Steps 5-9 under approved plan]
    N -->|Bug fix| V[Step 5: Match local style and inspect nearby code]
    V --> W{API change required?}
    W -->|Yes| AA([STOP and request API-change approval])
    W -->|No| AB[Step 6: Implement root-cause fix]
    U --> V
    AB --> AC[Step 7: Add and run focused tests]
    AC --> AD[Step 8: Review full diff and verify behavior]
    AD --> AE[Step 9: Do not push]
    AE --> Y
    Y -->|Yes| H
    Y -->|No| AF[Final report: per-repo results and cross-repo summary]
    O --> AF
    Z --> AF
    X --> AG([STOP and report blocker])
```

Progress updates are emitted before and after steps, outside the final report. A feature plan and an API-change request are approval stops; neither proceeds to source edits until approved.
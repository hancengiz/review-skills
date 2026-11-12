# Mermaid report

Use when Archify is unavailable and the user declines to install it, or when
`--mermaid` is given. Produce Markdown the repository already renders:
`docs/reviews/<slug>/README.md` as the overview and one
`<machine>.md` per machine, same five sections as the Archify pages.

## Before diagram

Name states by their column pattern; mark implicit ones in the label and put
the writer on the transition.

```mermaid
stateDiagram-v2
    direction LR
    prepared --> start_dispatched : transition()
    start_dispatched --> dialing : connect
    dialing --> connected : hello
    connected --> disconnected : close
    connected --> routable : route
    disconnected --> exited : recordRunnerProcessExit
    start_dispatched --> outcome_unknown : sweep (SQL CASE)
    start_dispatched --> ghost : no CHECK
    state "“dialing” · same status ∧ pending_connection_id set" as dialing
    state "“routable” · connected ∧ routable_at set" as routable
    state "“exited” · failure_code = runner_process_exited" as exited
    state "used_at ∧ no pending · filtered by attachable()" as ghost
    note right of ghost : representable, never named
```

## After diagram

```mermaid
stateDiagram-v2
    direction LR
    [*] --> Prepared
    Prepared --> StartDispatched : dispatch
    StartDispatched --> Dialing : dial
    Dialing --> Connected : hello
    Connected --> Connected : routeAnnounced
    Connected --> Disconnected : socketClosed
    Disconnected --> Dialing : redial
    StartDispatched --> OutcomeUnknown : deadlinePassed
    OutcomeUnknown --> Failed : processExited
    Connected --> Revoked : revoke
    Disconnected --> Expired : expire
    Failed --> [*]
    Revoked --> [*]
    Expired --> [*]
```

Keep each diagram under ~12 states; split a bigger machine into the main path
and its exits. Put the data each state carries in a table under the diagram
rather than in the state label.

## Overview

Ranked Markdown table (machine, owner, scatter today, representable illegal
state, payoff, risk); an `xychart-beta` bar chart of scattered writers versus
re-derived predicates when the renderer supports it, otherwise a table; the
"from columns to a reducer" steps as a numbered list; already-explicit list;
lower-payoff findings; a task list to decide.

## Code section

Quote the real excerpt with its path and lines in the fence title
(`` ```ts title="packages/.../runner-admission-store.ts:187-190" ``) and the
proposed code in a fence titled `(proposed)`. Keep the state union, the event
union and `apply()` complete enough to read; elide remaining rows with a
comment.

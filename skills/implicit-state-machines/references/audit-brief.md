# Audit brief

Run one read-only subagent per slice, all in the same message so they run in
parallel. Replace the bracketed parts. Keep the brief verbatim otherwise; it is
tuned to return evidence, not opinions.

## Slices

Split by ownership so no two agents read the same files. A typical split:

| Slice | Scope |
|---|---|
| Core domain and execution | the entities that carry work: orders, jobs, sessions, turns, dispatches, queues, connections |
| Infrastructure and background | provisioning, leases, retries, schedulers, inboxes, integrations and OAuth, invitations, job runners |
| Clients | web or mobile stores, SDK connection and subscription state, long-lived CLI or daemon processes |

Point each agent at the schema (migrations, ORM models, table definitions) and
at the documents that state intended lifecycles. Exclude archived or vendored
folders explicitly.

## Prompt

```
Read-only audit of the repository at [REPO] (do NOT edit files, do NOT run
tests/builds/servers; skip [EXCLUDED FOLDERS]).

Goal: find IMPLICIT state machines — lifecycles whose states and transitions
are real but not modelled in one place: status encoded across several nullable
columns/timestamps/booleans, status string columns mutated by scattered
`UPDATE ... SET status =` in many files, transition guards duplicated across
callers, illegal combinations representable, recovery/retry logic that
re-derives the state from evidence. I want ones where making the machine
explicit (single transition table/reducer, typed states, one owner) would
drive concrete improvements (bugs removed, duplicated guards deleted, recovery
simplified, observability).

Your scope: [SLICE]. The schema is under [SCHEMA PATH]. Docs: [DOCS].

Also note, briefly, lifecycles that are ALREADY explicit and well-modelled
(so I don't propose them) — anything with a transition table or
discriminated-union reducer.

Return at most 6 candidates, ranked by improvement payoff. For each, be
concrete and terse:
- name, owner package
- the states as they exist today and HOW they are encoded (columns/flags; file:line)
- where transitions happen (3-6 file:line sites showing the scatter)
- evidence of pain: duplicated guards, representable illegal states,
  repair/reconcile code, comments describing races, tests for odd combos (file:line)
- proposed explicit machine: states + events list (short)
- concrete improvement it buys
- confidence (high/med/low) and what you did not verify
Do not pad; verify claims by reading the code, and say plainly when something
is a guess.
```

## After the agents return

- Spot-check: open the schema lines and one scatter site per top candidate.
  An agent's confident `file:line` can be off by a few lines; fix it.
- Merge duplicates across slices; a machine found twice ranks first.
- Keep the "already explicit" lists; they go into the overview as proof the
  codebase knows the pattern.
- Note what no agent verified, for the scope line.

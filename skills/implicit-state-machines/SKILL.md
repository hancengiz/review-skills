---
name: implicit-state-machines
description: Audit a codebase for implicit state machines (lifecycles encoded in nullable columns, boolean flags and scattered status guards) and report the ones worth making explicit as a visual before/after review with code sketches. Use when asked to find hidden or implicit state machines, lifecycle smells, status-column sprawl, or where a reducer or transition table would remove duplicated guards. Renders with Archify when it is installed, otherwise with Mermaid in Markdown.
argument-hint: "[scope: packages or paths] [--mermaid]"
---

# Implicit state machines

Find lifecycles whose states and transitions are real but not modelled in one
place, then show, per machine, how it is encoded today and what the explicit
version looks like. Output is a review the reader can act on: ranked table,
before/after state diagrams, the files that change, a code sketch.

## When

- "Find implicit state machines", "lifecycle audit", "where would a state
  machine help", "status column sprawl", "duplicated guards".
- As preparation for a refactor that introduces a reducer or transition table.

Not for designing a new machine from requirements.

## What counts as implicit

Flag a lifecycle when at least two hold:

- a `status` column plus nullable timestamps or booleans that together encode
  the phase (`failed ∧ ready_at IS NULL` means something different from
  `failed ∧ ready_at IS NOT NULL`);
- transition guards (`status IN (...)`, `if (a && !b && c)`) copied across
  callers, application code and SQL;
- illegal combinations the schema allows and a read-time predicate filters;
- recovery or reconcile code that re-derives the state from evidence;
- a comment describing a race, or tests that enumerate odd combinations.

Do not flag lifecycles that already have one owner: a transition table, a
discriminated-union reducer, a value object that validates its transitions,
or CHECK constraints generated from the states. List those as "already
explicit"; they prove the codebase knows the pattern.

## Steps

1. **Scope and freeze.** `git status --short --branch`; record `HEAD` and the
   origin. Split the codebase into 2–3 audit slices by ownership (for example:
   core domain and execution; infrastructure and background jobs; clients).
2. **Audit.** Run one read-only subagent per slice with the brief in
   [references/audit-brief.md](references/audit-brief.md). Each returns at most
   six candidates with `file:line` evidence and the already-explicit list.
   Without subagents, walk the slices yourself with the same brief.
3. **Verify and rank.** Spot-check every top candidate's encoding and at least
   one scatter site yourself. Rank by payoff: duplicated guards removed,
   illegal states made unrepresentable, recovery simplified, bugs surfaced.
   A machine two slices found independently ranks first. Keep six at most.
4. **Choose the renderer.** Archify when `node <archify>/bin/archify.mjs doctor`
   reports ready (look for an `archify` skill directory of the current agent,
   such as `~/.agents/skills/archify`). Otherwise ask once whether to install
   it with the user's consent:
   `npx -y skills@latest add https://archify.si --skill archify --agent <agent> --global --copy --yes`
   or to use Mermaid. `--mermaid` in the arguments skips the question.
5. **Build the report.** Follow [references/archify-report.md](references/archify-report.md)
   or [references/mermaid-report.md](references/mermaid-report.md). One
   overview plus one page per machine, each with the five sections below.
6. **Show and file.** Serve the output folder on a loopback port
   (`python3 -m http.server --bind 127.0.0.1 <port>`) and link the index, or
   open the HTML directly. When asked to commit, put the pages and their
   sources under a reviews folder such as `docs/reviews/<slug>/` with a README
   that states the revision audited and that nothing is implemented.

## Page sections, in order

1. **Answer**: one paragraph with the numbers, up to four reasons, scope line.
2. **How is the state encoded today?** Before diagram: states as column
   patterns, with `implicit` and `representable` tags on the ones no status
   names; transitions labelled with the writer that performs them.
3. **What does the explicit machine look like?** After diagram: named states
   carrying their data, events as transition labels, failures returning.
4. **What it buys** and **Files that change**: facts list and a three-column
   table (file, today, after).
5. **Code: before and after**: a real excerpt pinned to the revision, then the
   proposed union, events, `apply(state, event)` and the one store or reader
   change it implies. Sketches, not drop-in code; say so.

Fewer words, big visuals: a diagram replaces the paragraph that would describe
it. Counts come from reading; say so in scope.

## References

- [references/audit-brief.md](references/audit-brief.md): the subagent prompt and slice guidance.
- [references/archify-report.md](references/archify-report.md): lifecycle JSON conventions, explainer skeleton, finalize commands, repair rules.
- [references/mermaid-report.md](references/mermaid-report.md): the same report as Markdown with `stateDiagram-v2`.
- [assets/](assets/): explainer and lifecycle templates to copy.

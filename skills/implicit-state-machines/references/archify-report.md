# Archify report

Read the Archify skill's `SKILL.md` and `references/authoring-defaults.md`
once; this file holds only what the review needs. Work in a scratch folder
outside the repository, for example `~/archify-work/.archify/<slug>/`, so
nothing lands in the checkout until the user asks for a commit.

## Files

```
<slug>/
  candidate.archify.md            overview explainer
  <machine>.archify.md            one per machine
  <machine>-before.lifecycle.json
  <machine>-after.lifecycle.json
  *.html                          finalize output
```

Copy `assets/machine.archify.md`, `assets/before.lifecycle.json` and
`assets/after.lifecycle.json` and replace every placeholder.

## Lifecycle diagrams (schema v3)

- `mainPath`: the happy path, 3–5 states. Six main-path states with labels over
  ~8 characters exceed the 1395px readable width.
- Before: `label` is the stored status or the column pattern (`“dialing”`),
  `sublabel` the columns that encode it (`∧ pending_connection_id set`),
  `tag` one of `implicit` / `representable` / `mirror` / `divergent`.
  Transition labels name the writer (`replaceAdmission ×3`, `sweep CASE`).
  Use `variant: "security"` for illegal or unfenced paths, `dashed` for repair.
- After: `label` is the state name, `sublabel` its data (`{ connection, routable }`),
  `step` numbers the main path. Transition labels are event names.
- Keep `sublabel` under ~26 characters and labels short: the validator fails on
  `lifecycle/state-text-too-long` and `lifecycle/too-wide`; shorten text, do not
  add geometry.
- Two cards per diagram: before = "Where the state lives" and "Re-derived
  elsewhere"; after = "One owner" and "Buys".

Finalize each:

```bash
node <archify>/bin/archify.mjs finalize lifecycle <m>-before.lifecycle.json <m>-before.html --quality showcase --json
```

## Machine pages

The explainer draft has the five sections from `SKILL.md`. Rules that bite:

- IDs share one namespace across panels, diagrams, steps and blocks.
- A literal `|` inside a table cell is `\|`; use "or" in prose instead.
- Sentences over 28 words warn; split them.
- `@source <id> title="…" path=<repo-relative> lines=a:b` then
  `@code source=<id> language=ts` embeds the pinned excerpt; `--repo-root`
  must point at the repository and the lines must exist at `HEAD`.
- The "after" code goes in a plain fence with `title="<file> (proposed)"`.
- Native diagrams: `@diagram lifecycle id=<m>-before-d source=<m>-before.lifecycle.json title="Before"`;
  at most four per page.

```bash
node <archify>/bin/archify.mjs finalize explainer <m>.archify.md <m>.html --repo-root <repo> --json
```

## Overview page

Answer with the totals; a ranked Markdown table (machine, owner, scatter today,
representable illegal state, payoff, risk) with `{good:…}` / `{caution:…}` on
the deciding cells; one `xychart-beta horizontal` of scattered writers versus
re-derived predicates per machine; a `@workflow` "from columns to a reducer"
with `@steps`; `@facts` for already-explicit lifecycles; a short list of
lower-payoff findings; a task-list "Decide" panel.

## Serving and checking

Copy the HTML into a clean folder with an `index.html` that links every page,
serve it on a loopback port, and screenshot one page with Playwright when it
is available before claiming it renders. Report gates honestly: lifecycle
finalize includes a browser check; explainer finalize does not.

# review-skills

Agent skills that review a codebase and hand back a decision-ready report:
ranked findings, before/after diagrams, the files that change, and code
sketches. Each skill works with Claude Code, Codex, Cursor, OpenCode and
GitHub Copilot through the [skills](https://skills.sh) CLI.

## Install

```bash
npx skills add hancengiz/review-skills
```

One skill only:

```bash
npx skills add hancengiz/review-skills --skill implicit-state-machines
```

Add `--global` for every project, `--agent claude-code` (or `codex`, `cursor`,
`opencode`, `github-copilot`) to pick agents, `--list` to see what is in the
repository first.

## Skills

| Skill | Ask for | Output |
|---|---|---|
| [implicit-state-machines](skills/implicit-state-machines/SKILL.md) | "find implicit state machines", "lifecycle audit", "status column sprawl" | Ranked machines; per machine a before/after state diagram, what it buys, files that change, and a code sketch of the explicit reducer |

Planned: `code-review`, `architecture-review`. Contributions welcome; keep each
skill self-contained under `skills/<name>/` with a `SKILL.md` under 200 lines
and longer material in `references/`.

## Rendering

Reports render with [Archify](https://archify.si) (interactive standalone
HTML) when it is installed. Without it, the skill asks once whether to install
Archify or to fall back to Mermaid diagrams in Markdown; pass `--mermaid` to
skip the question.

## Example

A run of `implicit-state-machines` on a TypeScript monorepo found six
lifecycles, among them a connection admission stored as one status column
crossed with eleven nullable columns, with "routable" recomputed in nine
places. The page for it shows the column patterns as states, the proposed
typed union, and the single `transition()` write path.

## License

MIT

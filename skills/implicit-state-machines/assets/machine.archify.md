# N · <Machine>: <one-line encoding smell> {#<slug>}

<One paragraph: what the state is crossed with, the counts in bold, what one owner removes.>[](source:<src1>)

- <Reason with a citation.>[](source:<src2>)
- <Reason.>
- <Reason.>
- <Reason.>

@answer scope="<tables or files in scope> · <what stays unchanged> · counts from reading"
@meta locale=en style=product
@source <src1> title="<what it shows>" path=<repo-relative path> lines=<a>:<b>
@source <src2> title="<what it shows>" path=<repo-relative path> lines=<a>:<b>
@source <code-before> title="<function> today" path=<repo-relative path> lines=<a>:<b>

## How is the state encoded today? {#<slug>-before}

@diagram lifecycle id=<slug>-before-d source=<machine>-before.lifecycle.json title="Before"

## What does the explicit machine look like? {#<slug>-after}

@diagram lifecycle id=<slug>-after-d source=<machine>-after.lifecycle.json title="After"

## What it buys {#<slug>-buys}

@facts
| Deleted | <guards, copies, repair code> |
| Unrepresentable | <the illegal combinations> |
| Observability | <what becomes a named state or log line> |
| Risk | <low/medium/high: why> |

## Files that change {#<slug>-files}

| File | Today | After |
| --- | --- | --- |
| `<path>` | <what it does now> | <what it does after> |

## Code: before and after {#<slug>-code}

Today <one sentence on the excerpt>:

@code source=<code-before> language=ts

After: <one sentence>.

```ts title="<new or changed file> (proposed)"
export type <State> =
  | { kind: "<a>" }
  | { kind: "<b>"; <data> };

export type <Event> = { type: "<e1>" } | { type: "<e2>"; <data> };

export function apply(s: <State>, e: <Event>): <State> {
  switch (`${s.kind}/${e.type}`) {
    case "<a>/<e1>": return { kind: "<b>", <data> };
    // …remaining rows; anything else is illegal
    default: throw new <State>TransitionError(s.kind, e.type);
  }
}
```

```ts title="<store or reader> (proposed)"
// the one write path or the one reader that replaces the scattered guards
```

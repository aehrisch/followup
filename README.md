# Follow Up

A single-file web app for the things you discover while doing something else.

Working on A, you notice B. You want to finish A first, but you do not want to lose B. Push B onto a stack. Come back later and draw from the top.

It is not a todo list. Items are expected to live for hours or days, not weeks. The board should be nearly empty at the end of the day.

## Features

- Several **stacks** side by side, one per context. Create with `s`, rename by clicking the name.
- **Items** are Markdown. Click to edit, blur or `Enter` to render. `Shift+Enter` inserts a newline, `Esc` cancels.
- New items are pushed on **top** (LIFO). `n` pushes onto the current stack (the one you last touched, outlined).
- **Select** a card with the arrow keys (from nothing selected, they start at the top of the current stack), or click its footer. `Esc` while editing leaves the card selected.
- **Blockers** (`b` on a focused card, `⌘/Ctrl+B` while editing) are red and mark that you are waiting for something external. They are never swept.
- **Reorder** by drag and drop, or `Alt+Arrows` on a focused card (up/down within a stack, left/right across stacks).
- **Age**: every card shows how old it is. Items older than the *stale after* threshold fade out. *Sweep* deletes them after a confirming second click.
- `✓` (or `⌘⌫` / `Ctrl+Backspace` on a selected card) marks an item done and removes it. The last 20 finished items show in a scrolling row at the bottom (newest left); older ones are dropped. Double-click one (or `Enter`) to put it back on top of its stack. Sweep and deleted stacks don't land there.

## Running it

Open `index.html` in a browser. That is all.

State lives in `localStorage` under the key `followup.v1`, so it is bound to the browser profile and the origin (file path or host) you open it from. Serving it from a web server keeps the data stable across machines only if the URL is the same; there is no sync.

## Dependencies

Loaded from cdnjs at runtime, pinned:

- [marked](https://github.com/markedjs/marked) 12.0.2 for Markdown
- [DOMPurify](https://github.com/cure53/DOMPurify) 3.1.6 to sanitise the rendered HTML

Fonts (Fraunces, Figtree, JetBrains Mono) come from Google Fonts with system fallbacks. Without network access the app still works; it just falls back to system fonts and renders item text as plain text if `marked` is missing.

## Data format

```json
{
  "maxAgeDays": 3,
  "stacks": [
    {
      "id": "…",
      "name": "Context A",
      "items": [
        { "id": "…", "text": "markdown", "blocker": false, "created": 1761500000000, "updated": 1761500000000 }
      ]
    }
  ],
  "done": [
    { "…item fields": "…", "stack": "Context A", "stackId": "…", "finished": 1761600000000 }
  ]
}
```

## Export / import

**Export** downloads everything (stacks, items, recently closed, the *stale after* setting) as `followup-YYYY-MM-DD.md`. Before downloading, the file is re-imported in memory and compared with the board; if anything would be lost, the export is aborted with an error.

**Import** reads such a file and, after a confirmation, replaces the whole board. If the file cannot be parsed, an error names the line and nothing changes.

```markdown
# Stacks
<!-- followup {"maxAgeDays":3} -->

## Context A

### Item 1
<!-- followup {"blocker":false,"created":"2026-10-26T09:12:33.123Z","updated":"2026-10-26T09:12:33.123Z"} -->

The item text, as Markdown.

## Other

### Recently Closed

#### Item 1
<!-- followup {"blocker":false,"created":"…","updated":"…","stack":"Context A","finished":"…"} -->

A finished item.
```

- Item headings (`Item 1`, …) are labels only; the text below them is the item.
- `## Other` followed by `### Recently Closed` is the closed section; a stack may still be called *Other*.
- Lines of item text that start with `#` are written with one extra leading `\`, so they cannot be mistaken for structure; import removes it.
- The `followup` comments are optional when writing a file by hand. Missing dates default to now, `blocker` to false. Any other heading level or text outside an item is an error.
- Ids are regenerated on import. Restoring a closed item matches its stack by name.

## Roadmap

Iterated as needed. Candidates: a global capture shortcut, a tiny always-on-top window.

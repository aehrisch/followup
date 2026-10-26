# Follow Up

A single-file web app for the things you discover while doing something else.

Working on A, you notice B. You want to finish A first, but you do not want to lose B. Push B onto a stack. Come back later and draw from the top.

It is not a todo list. Items are expected to live for hours or days, not weeks. The board should be nearly empty at the end of the day.

## Features

- Several **stacks** side by side, one per context. Create with `s`, rename by clicking the name.
- **Items** are Markdown. Click to edit, blur or `Enter` to render. `Shift+Enter` inserts a newline, `Esc` cancels.
- New items are pushed on **top** (LIFO). `n` pushes onto the stack you last touched.
- **Blockers** (`b` on a focused card, `⌘/Ctrl+B` while editing) are red and mark that you are waiting for something external. They are never swept.
- **Reorder** by drag and drop, or `Alt+Arrows` on a focused card (up/down within a stack, left/right across stacks).
- **Age**: every card shows how old it is. Items older than the *stale after* threshold fade out. *Sweep* deletes them after a confirming second click.
- `✓` marks an item done and removes it. There is no archive by design.

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
  ]
}
```

## Roadmap

Iterated as needed. Candidates: export/import as a Markdown file, a global capture shortcut, a tiny always-on-top window.

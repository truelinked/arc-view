# ARC Viewer

A fast, keyboard-driven viewer for large XML and JSON. Paste or open a file and ARC Viewer renders it as a collapsible tree — built to stay smooth on documents that make “view source” and most browser extensions choke.

**Live:** [view.arc.host](https://view.arc.host/)

Everything runs in your browser. Nothing is uploaded, and there is no backend — it’s a single static HTML file.

## Why

Open a huge sitemap or a big JSON API dump in a browser tab and you get a wall of unstyled text, or an extension that spins for ten seconds and then stutters as you scroll. ARC Viewer parses the document in memory and only ever renders the handful of rows visible on screen, so a tree with hundreds of thousands of nodes scrolls, searches, and expands without lag.

## Features

- **XML and JSON** in one tree view, with type-aware colouring (strings, numbers, booleans, null, tags, attributes).
- **Virtualized rendering** — only the rows in the viewport are in the DOM, so file size barely affects scroll performance.
- **Keyboard-first.** Navigate, expand, collapse, search, and open files without touching the mouse.
- **Fast search** across keys and values, substring or `*` / `?` wildcards, with jump-to-match and match highlighting.
- **Collapsed-node previews.** A collapsed node shows its first couple of child values inline, so a list of near-identical `<url>` entries is scannable without expanding each one.
- **Copy anything** — `c` copies the selected node's value in full (untruncated), `Shift`+`C` copies it as JSON or XML, `p` copies its path. Double-click a row, or use the copy button that appears on hover.
- **Paste anywhere** (`⌘V` / `Ctrl+V`) to open the clipboard as a new document.
- **Session history** (`h`) of everything you’ve opened this session, with restore.
- **Helpful errors** — invalid JSON reports the line, column, and a caret at the fault; malformed XML still renders a best-effort tree with a warning; JSON Lines / NDJSON is detected and parsed automatically.
- **Light and dark** themes, following your system preference.

## Getting started

Just open [view.arc.host](https://view.arc.host/), then:

- **Open a file** — press `o` or click **Open file…** (`.json`, `.xml`).
- **Paste** — copy JSON or XML from anywhere and press `⌘V` / `Ctrl+V`.
- **Drag and drop** a file onto the window.
- Or click **Sample JSON** / **Sample XML** to explore with generated data.

## Keyboard shortcuts

| Key | Action |
| --- | --- |
| `↑` `↓` | Move selection |
| `→` | Expand, or step into the first child |
| `←` | Collapse, or jump to the parent |
| `⏎` / `Space` | Toggle the selected node |
| `Shift`+`→` / `Shift`+`←` | Expand / collapse the whole subtree |
| `c` / `⌘C` / `Ctrl+C` | Copy the selected node's value (a container copies its whole subtree) |
| `Shift`+`C` | Copy the selected node as JSON / XML |
| `p` | Copy the path to the selected node |
| `Home` / `End` | First / last node |
| `PgUp` / `PgDn` | Page up / down |
| `⌘K` / `Ctrl+K` / `/` | Search |
| `⏎` / `Shift`+`⏎` | In search: next / previous match |
| `⌘V` / `Ctrl+V` | Paste clipboard as a new document |
| `o` | Open a file |
| `h` | History |
| `?` | Keyboard help |
| `Esc` | Close search / any panel / dismiss a message |

## Supported formats

- **JSON** — objects, arrays, and primitives.
- **JSON Lines / NDJSON** — auto-detected when standard JSON parsing fails but each line is valid JSON; loaded as an array.
- **XML** — elements, attributes, text, CDATA; comments and processing instructions are skipped. The parser is tolerant: a malformed document still produces a best-effort tree, with a warning describing what looked wrong (unclosed or mismatched tags).

Well-formed HTML can be viewed through the XML path, but loosely-written HTML (unclosed tags, etc.) will only render best-effort.

## How it works

The document is parsed once into an in-memory node model. The tree you see is a *flattened* list of only the currently-visible nodes; expanding or collapsing splices a range into that list rather than rebuilding it. Rendering draws only the rows inside the viewport (plus a small overscan), positioned with a single transform, so the DOM never holds more than a screenful of rows regardless of document size. Long values are clamped for display (the full value is kept for search).

Because rows are recreated on every scroll and long values are clamped, native text selection would be both fragile and lossy — so it stays off, and copying reads out of the node model instead. That means `c` always yields the complete value, and copying a container re-serializes its subtree back to real JSON or XML.

## Privacy

ARC Viewer is fully client-side. Files and pasted content never leave your browser — there is no server, no upload, no analytics on your data. History is kept in memory for the current session only and is cleared when you close or reload the tab.

## Running locally

It’s a single file with no build step and no dependencies:

```
# clone, then just open index.html in a browser — or serve it:
python3 -m http.server 8000
# → http://localhost:8000
```

## Deployment

Hosted on GitHub Pages from the `main` branch (root), with a custom domain set via the `CNAME` file and a `view` DNS record pointing at GitHub Pages.

Favicon files live in the repo root (`favicon.svg`, `favicon.ico`, and optionally `apple-touch-icon.png`) and are referenced from `index.html`.

## Limitations

- The whole file is read into memory, so extremely large documents (well into the hundreds of MB) are bounded by available RAM.
- Parsing is synchronous; a very large paste briefly blocks the tab while it parses.
- History does not persist across reloads (session only).

## License

MIT — see [LICENSE](LICENSE). <!-- add a LICENSE file, or change this line to match your intent -->

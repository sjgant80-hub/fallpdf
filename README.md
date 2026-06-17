# ◊ FallPDF · sovereign PDF writer

> The Acrobat wedge. Write in markdown · HTML · or plain text → real `.pdf` file. Single HTML, no server, no subscription.
>
> v1 · prime **1427** · MIT · ◊·κ=1

**Live:** [sjgant80-hub.github.io/fallpdf](https://sjgant80-hub.github.io/fallpdf/)
**Source:** [github.com/sjgant80-hub/fallpdf](https://github.com/sjgant80-hub/fallpdf)

Phase-1 tool of the **FallStudio** plan — the sovereign Adobe Creative Cloud alternative being shipped one HTML file at a time.

---

## What it does

Three-pane editor: markdown source on the left, live preview in the middle, page setup on the right. **↓ PDF** generates a real PDF/1.4 document with multi-page layout, headers, footers, page numbers, and images — entirely in your browser. Nothing uploaded.

### Source modes

| | Mode | Input |
|---|---|---|
| **md** | Markdown | `# ## ### ` headings, `**bold**`, `*italic*`, `` `code` ``, `> quote`, `- bullets`, `1. numbered`, `---` rules, `![alt](url)` images, fenced code blocks |
| **html** | HTML | paste any HTML — headings, paragraphs, lists, blockquotes, pre, img, hr all map to PDF |
| **text** | Plain text | each blank-line-separated chunk becomes a paragraph |

### Page setup

- Size: **A4 · Letter · Legal · A3 · A5 · business card · custom (mm)**
- Orientation: portrait / landscape
- Margins: top / bottom / left / right (mm)
- Font: **Helvetica** (sans), **Times** (serif), **Courier** (mono) — PDF built-ins, no embedding
- Body size and line height
- Header text + page numbers in footer
- PDF metadata: title and author

### Export

- **↓ PDF** — generates a true PDF file (PDF/1.4 spec, no library, all inline)
- **↓ .md** — save the source markdown
- **⎙ print** — open the browser print dialog using the live preview

### Templates (built-in · T0)

The Ω palette knows: **letter · memo · invoice · report · CV**. One click loads a starter you fill in.

### Ω autopilot (Ctrl+K)

- *"memo to Mansoor about Friday's audit review"* → loads the memo template
- *"a4 landscape"* / *"letter portrait"* → switches the page setup
- T3 (BYOK Anthropic / Gemini *free* / OpenAI / OpenRouter): *"draft a one-page brief on sovereign tools for an enterprise audience"* → Ω returns markdown, drops it into the editor

---

## Use it

1. Open `index.html` from `file://` or visit the live URL
2. Type in markdown (or paste HTML, or load a `.md` / `.txt` / `.html` file)
3. Adjust page setup on the right
4. **Ctrl+K** for autopilot · or pick a template
5. **↓ PDF** to download

Auto-saves source + settings to IndexedDB. Sample doc loads on first visit.

## Keyboard

- **⌘E / Ctrl+E** or **⌘S / Ctrl+S** — export PDF
- **Ctrl+K** — Ω autopilot
- **Esc** — close palette / modal

## Sovereignty stack

| Layer | Status |
|---|---|
| **UI** | runs from `file://` |
| **Compute** | T0 generates PDFs in-browser · T3 your direct API call (Ω drafting only) |
| **Storage** | IndexedDB for source + settings · PDF downloads to your device |
| **Mesh** | `BroadcastChannel('fall-signal')` · prime 1427 · responds to ping · `postMessage` API for `setSource` and `exportPdf` |

---

## What this v1 does NOT do — and what's planned

**v1 scope:** PDF *writer*, not Reader. Generates new PDFs from your source.

**What's not in v1:**

| Feature | Reason | Plan |
|---|---|---|
| **Read / open external PDFs** | requires PDF.js (~500KB+ bundled) | v2 — toggleable lazy-load |
| **Merge / split arbitrary PDFs** | needs parser for external PDFs | v2 with pdf-lib (~250KB) lazy-loaded |
| **Annotate existing PDFs** | needs view layer first | v2 |
| **Fillable form fields (interactive)** | AcroForm dictionaries | v2 |
| **Tables in markdown** | parser exists, layout engine for tables is non-trivial | v1.1 |
| **Custom font embedding** | font subsetting requires a TTF parser | v2 — current fonts are the 14 PDF built-ins (3 exposed) |
| **Digital signatures** | could pair with Konomi Ed25519 shim | future · Konomi-natively |

70% of what people use Acrobat for is *create* / *export*. That's v1. The rest is v2 with pdf-lib bundled.

---

## For developers

```
index.html      one file · ~46KB · vanilla JS · no deps · zero CDN
README.md       this
LICENSE         MIT
.nojekyll       Pages legacy deploy
```

### Architecture

- **Parser:** `parseMd(src)` returns a stream of typed blocks (`h`, `p`, `quote`, `code`, `ul`, `ol`, `hr`, `img`)
- **PDF writer:** `buildPdf()` lays the blocks into pages (Y-from-top, with page-break on margin overflow), then `assemblePdf()` serializes to PDF/1.4 binary: catalog, pages tree, info dict, 6 font objects (Helvetica/Times/Courier in regular + bold), one XObject per embedded image (JPEG-encoded), per-page content streams with `BT...ET` text blocks and `cm` image placements, xref table, trailer.
- **Wrap:** approximate Helvetica/Times/Courier widths via `FONT_WIDTH[font] = 0.5/0.5/0.6` ems. Good enough for most copy; long URLs and unusual glyphs may overhang — Phase 2 will use AFM tables.
- **Images:** `<img>` loaded → drawn to canvas → `toDataURL('image/jpeg', 0.85)` → bytes embedded with `/DCTDecode` filter.
- **Cascade:** Ω drafts via Anthropic/Gemini/OpenAI/OpenRouter; same pattern as FallOffice, FallMage, ACG Mapper, FallMap.

### Adding a template

Add to the `TEMPLATES` object — markdown string keyed by trigger word. The Ω router matches the trigger in the user's prompt.

### Adding a page size

Add to the `sizes` map in `getPageDims()` and an `<option>` in `#pgSize`. Sizes are `[wMm, hMm]`; orientation handles the rotate.

### Adding a markdown feature

`parseMd` produces blocks; both `mdToHtml` (preview) and the layout loop in `buildPdf` (export) handle the rendering. Add a `b.t === 'newkind'` branch to both.

## Credit

- Same cascade pattern as [FallOffice](https://github.com/sjgant80-hub/falloffice), [FallMage](https://github.com/sjgant80-hub/fallmage), [ACG Mapper](https://github.com/sjgant80-hub/acg-mapper), [FallMap](https://github.com/sjgant80-hub/fallmap)
- PDF writer is hand-rolled to keep the file <50KB; nothing fancy, just enough of PDF/1.4 to render text and images cleanly
- Part of the FallStudio plan — Adobe Creative Cloud, sovereign, one HTML at a time

⚒ Part of the [fall* estate](https://github.com/sjgant80-hub) · prime 1427 · ◊·κ=1

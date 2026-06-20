# AGENTS.md

## Project purpose

This repository is a GitHub Pages audiobook reading site for weekly course readings. The site should feel like a clean, warm, book-style reading room. It must support:

- A home screen with a chapter list.
- Chapters that can contain subchapters/sections.
- Each chapter or subchapter may have its own exact reading text, MP3 audiobook, and timing/highlight data.
- Weekly additions without rewriting old chapters.
- The current visual style: paper background, serif book text, sticky dark audio bar, sentence-level highlighting, click-to-seek, and auto-scroll.

Do not redesign the site into a generic web app. Preserve the current book aesthetic.

## Current design style to preserve

Use the existing `index.html` as the visual reference. Keep these design principles:

- Warm paper colors: cream, tan, muted brown, dark ink.
- Serif body text, preferably Georgia or Times New Roman.
- A centered book/card layout with generous padding.
- Sticky top audio control bar.
- Sentence-level highlighting with a soft yellow highlight.
- Clickable text segments that jump to the correct audio time.
- Mobile-friendly layout.
- No heavy frameworks unless absolutely necessary.
- Static files only; the site must work on GitHub Pages without a backend.

Preferred CSS variable palette from the current style:

```css
:root {
  --paper: #f6efe2;
  --paper-edge: #e7dac5;
  --ink: #24211d;
  --muted: #74695c;
  --accent: #9b6b43;
  --accent-soft: #f0d6a8;
  --active: #ffe8a3;
  --active-border: #c68b3c;
  --shadow: rgba(52, 37, 18, 0.18);
}
```

## Recommended future file structure

Refactor the current one-chapter site into this structure:

```text
/
├─ index.html                  # Home screen: chapter list
├─ chapter.html                # Reusable chapter reader page
├─ styles.css                  # Shared book-style CSS
├─ app.js                      # Shared reader logic
├─ chapters.json               # Table of contents / metadata
├─ AGENTS.md                   # Instructions for future agents
├─ README.md                   # Human upload instructions
└─ chapters/
   ├─ chapter-02-japan/
   │  ├─ chapter.json
   │  ├─ text.txt
   │  ├─ audio.mp3
   │  └─ timings.json
   ├─ chapter-03-example/
   │  ├─ chapter.json
   │  ├─ text.txt
   │  ├─ audio.mp3
   │  └─ timings.json
   └─ ...
```

For chapters with subchapters, use this structure:

```text
chapters/chapter-04-example/
├─ chapter.json
├─ section-01/
│  ├─ text.txt
│  ├─ audio.mp3
│  └─ timings.json
├─ section-02/
│  ├─ text.txt
│  ├─ audio.mp3
│  └─ timings.json
└─ section-03/
   ├─ text.txt
   ├─ audio.mp3
   └─ timings.json
```

Do not store all future chapter text directly inside `index.html`. Keep text and timing data in external files so weekly updates stay manageable.

## `chapters.json` format

Create or maintain a root-level `chapters.json` file like this:

```json
{
  "siteTitle": "Reading Audiobook Library",
  "courseTitle": "Gender and Sexuality in East Asia",
  "chapters": [
    {
      "id": "chapter-02-japan",
      "title": "Japan",
      "subtitle": "Early Modern East Asia Reading",
      "folder": "chapters/chapter-02-japan",
      "dateAdded": "2026-06-20",
      "status": "ready",
      "sections": [
        {
          "id": "full",
          "title": "Full Japan Section",
          "text": "chapters/chapter-02-japan/text.txt",
          "audio": "chapters/chapter-02-japan/audio.mp3",
          "timings": "chapters/chapter-02-japan/timings.json"
        }
      ]
    }
  ]
}
```

For chapters with subchapters:

```json
{
  "id": "chapter-03-example",
  "title": "Chapter 3 Title",
  "subtitle": "Optional subtitle",
  "folder": "chapters/chapter-03-example",
  "dateAdded": "2026-06-27",
  "status": "ready",
  "sections": [
    {
      "id": "section-01",
      "title": "Section 1 Title",
      "text": "chapters/chapter-03-example/section-01/text.txt",
      "audio": "chapters/chapter-03-example/section-01/audio.mp3",
      "timings": "chapters/chapter-03-example/section-01/timings.json"
    },
    {
      "id": "section-02",
      "title": "Section 2 Title",
      "text": "chapters/chapter-03-example/section-02/text.txt",
      "audio": "chapters/chapter-03-example/section-02/audio.mp3",
      "timings": "chapters/chapter-03-example/section-02/timings.json"
    }
  ]
}
```

## Chapter page behavior

`chapter.html` should read query parameters:

```text
chapter.html?chapter=chapter-02-japan&section=full
chapter.html?chapter=chapter-03-example&section=section-01
```

Expected behavior:

1. Load `chapters.json`.
2. Find the matching chapter and section.
3. Load the section text, audio, and timing file.
4. Render the exact text as clickable segments.
5. Highlight the current segment while audio plays.
6. Auto-scroll the active sentence into view.
7. Allow manual controls:
   - Back 10 seconds.
   - Forward 10 seconds.
   - Auto-scroll toggle.
   - Clear highlight.
   - Optional reading speed selector.
8. Provide a “Back to chapters” link.

## Home screen behavior

The root `index.html` should become the home screen, not a single chapter page.

It should show:

- Site title.
- Short subtitle.
- Chapter cards in a vertical list or responsive grid.
- Each chapter card should show:
  - Chapter title.
  - Subtitle or reading topic.
  - Date added.
  - Number of sections.
  - Status: ready / draft / missing audio.
- If a chapter has only one section, clicking the card opens that section directly.
- If a chapter has multiple subchapters, show a nested section list.

Keep the same book-style atmosphere: paper card, serif titles, warm colors.

## Timing format

Use sentence-level or paragraph-level timing. The current site uses sentence-level timing and this should remain the default.

Recommended `timings.json` format:

```json
[
  {
    "id": 1,
    "start": 0.0,
    "end": 10.7,
    "text": "Exact sentence or text segment here."
  },
  {
    "id": 2,
    "start": 10.7,
    "end": 18.4,
    "text": "Next exact sentence or text segment here."
  }
]
```

Rules:

- `start` and `end` are seconds.
- Segment text should match the visible text as closely as possible.
- Do not remove meaningful words from the reading.
- Keep OCR/page-number artifacts only if the user explicitly wants the text to remain exactly as extracted. Otherwise, ask before cleaning.
- If exact audio alignment is unavailable, use approximate sentence timing and label it honestly in README or metadata.

## Weekly append workflow

When adding a new weekly reading:

1. Create a new folder under `chapters/`.
2. Add `text.txt` with the exact reading text.
3. Add `audio.mp3`.
4. Add `timings.json`.
5. Add or update `chapter.json` if the chapter has extra metadata.
6. Add the chapter entry to root `chapters.json`.
7. Test locally by opening `index.html` and then the chapter link.
8. Confirm that audio plays, highlights move, and click-to-seek works.
9. Keep old chapter URLs working.

Do not overwrite older chapters unless the user explicitly asks.

## Text handling rules

- Preserve the exact reading text when the user asks for exact text.
- Do not summarize or paraphrase inside `text.txt`.
- Do not silently modernize spelling, punctuation, hyphenation, or page artifacts if “exact text” is required.
- If creating a cleaner reading version, save it separately as `text_clean.txt` and keep the original as `text.txt`.
- If the audio was generated from cleaned text, make sure `timings.json` follows the audio version, not a different source text.

## Audio handling rules

- Use MP3 for maximum GitHub Pages browser compatibility.
- Use simple relative paths, not absolute system paths.
- Keep filenames lowercase and stable when possible: `audio.mp3`, `text.txt`, `timings.json`.
- Avoid very large audio files if possible. If a file becomes too large for GitHub, split it by section.
- Never assume a browser can autoplay audio; require the user to press play.

## JavaScript implementation notes

Keep the reader logic simple and static-site friendly.

Recommended functions:

- `loadChapters()` — fetch `chapters.json`.
- `renderHome(chapters)` — render chapter cards.
- `getQueryParams()` — read `chapter` and `section`.
- `loadSection(chapterId, sectionId)` — fetch text/audio/timings.
- `renderSegments(segments)` — create clickable sentence spans.
- `findActiveIndex(currentTime)` — binary search current audio time.
- `updateHighlight()` — update `.active` and `.played` classes.
- `formatTime(seconds)` — display audio time.

Avoid overengineering. No React/Vue/Svelte unless the user specifically asks.

## Accessibility and usability

Maintain:

- Large readable text.
- Strong contrast between text and paper background.
- Keyboard-accessible buttons where possible.
- Clear current sentence highlighting.
- Mobile-friendly controls.
- A visible back link from chapter page to home.

Optional but useful:

- Font size controls.
- Reading speed controls.
- Dark mode toggle while preserving book style.
- Search within chapter.
- Progress memory using `localStorage`.

## GitHub Pages constraints

This must remain compatible with GitHub Pages:

- No server-side code.
- No database.
- No build step required unless the user explicitly asks for one.
- Use relative URLs.
- Test from a local static server when possible:

```bash
python3 -m http.server 8000
```

Then open:

```text
http://localhost:8000/
```

Fetching JSON may not work correctly from `file://`, so use a local server for testing.

## Current chapter migration instruction

The current Japan single-page site should be migrated into the new structure as:

```text
chapters/chapter-02-japan/text.txt
chapters/chapter-02-japan/audio.mp3
chapters/chapter-02-japan/timings.json
```

Use the existing files:

```text
Japan_part_exact_text.txt      -> chapters/chapter-02-japan/text.txt
Japan_reading_audiobook.mp3   -> chapters/chapter-02-japan/audio.mp3
timings.json                  -> chapters/chapter-02-japan/timings.json
```

The title should remain:

```text
Japan
```

The subtitle should remain:

```text
Early Modern East Asia Reading
```

## Quality checklist before finishing changes

Before handing work back to the user, check:

- Home page loads.
- Chapter list appears.
- Chapter card links work.
- Chapter page loads selected section.
- Audio source path is valid.
- Text appears in book style.
- Highlighting follows audio.
- Clicking a sentence seeks audio.
- Auto-scroll toggle works.
- Mobile layout still looks good.
- GitHub Pages uses only relative paths.

## Communication style for future agents

The user prefers practical, directly usable outputs. When reporting changes:

- Be concise.
- Provide the changed files.
- Mention exactly how to upload to GitHub Pages.
- Avoid long theoretical explanations.
- If timing is approximate, say so clearly.

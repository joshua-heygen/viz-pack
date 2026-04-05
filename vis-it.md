---
name: vis-it
description: >
  CRITICAL — if the user's message contains the phrase "vis it", you MUST use this skill.
  Stop everything and read this SKILL.md immediately.
  "vis it" is a command that means "help me understand this content using visualization."
  The input can be a URL, uploaded document, pasted text, meeting transcript, or a reference
  to something discussed in conversation. Pick the best visual format — diagram, flowchart,
  interactive explainer, structural map, data chart, timeline, comparison grid, or concept map —
  based on the content. The output is ALWAYS a visual rendered inline in chat using the
  Visualizer (show_widget). Do NOT respond with plain text summaries. Do NOT publish to
  HeyGenverse (that's what "ver it" is for). Do NOT use Excalidraw (that's what "diagram it"
  is for). Always produce a Visualizer widget — no exceptions.
---

# Vis It — Inline Visual Explainer

Turn any content into the best-fit visual for understanding it, rendered inline in chat.

## Trigger

The user says **"vis it"** and provides content via:
- A URL (article, blog post, documentation page)
- An uploaded file (PDF, DOCX, TXT, MD, CSV)
- Pasted text or a transcript
- A reference to a recent conversation topic

## Workflow

### Step 1: Acquire the content

Determine the content source and extract text:

| Source | Method |
|--------|--------|
| URL | Use `web_fetch` to retrieve the page. If blocked (429/403), use `web_search` with key phrases to get the content from search snippets. |
| Uploaded file | Read from `/mnt/user-data/uploads/` using the appropriate method (pdfplumber for PDF, python-docx for DOCX, pandas for CSV, direct read for TXT/MD). |
| Pasted text | Use directly from the conversation. |
| Meeting transcript | Use Granola `get_meeting_transcript` or read from `/mnt/transcripts/`. |
| Conversation context | Pull from the current chat history. |

If the content is very long (>5000 words), distill to the core structure before visualizing.

### Step 2: Analyze and choose visual format

Read the content and identify what kind of visual will best help the user understand it. The Visualizer supports SVG and HTML — pick the format that fits:

| Content pattern | Visual format |
|----------------|---------------|
| Process, workflow, sequence, steps | **Flowchart** (SVG) — boxes and arrows showing flow |
| System, architecture, components | **Structural diagram** (SVG) — nested containers with labeled regions |
| Timeline, phases, chronological growth | **Interactive timeline** (HTML) — clickable phases with detail panel |
| Comparison, options, tradeoffs | **Card grid** (HTML) — side-by-side cards with key differences highlighted |
| Data, metrics, trends | **Chart + stats** (HTML with Chart.js) — metric cards + bar/line chart |
| Pipeline, funnel, conversion | **Kanban/pipeline** (HTML) — multi-column cards with status badges |
| Concept, mental model, framework | **Explainer** (HTML) — hero insight + supporting structure + interactive elements |
| Mixed/complex | **Dashboard-style** (HTML) — hero metric + stats row + chart + content cards |

When in doubt, default to the **dashboard-style** layout — it handles most content well.

### Step 3: Load the Visualizer module

Before creating any visual, ALWAYS call:

```
visualize:read_me
```

Load the appropriate module(s): `diagram` for SVG flowcharts/structural, `interactive` for HTML explainers, `chart` for Chart.js data viz.

### Step 4: Build the visual

Create the visual using `visualize:show_widget`. Follow all Visualizer design rules:

#### Core principles
- **Seamless** — should feel like a natural extension of the chat
- **Flat** — no gradients, shadows, or decorative effects
- **Compact** — show the essential inline, explain the rest in text
- **Text in response, visuals in the tool** — all explanatory prose goes OUTSIDE the tool call

#### Content extraction rules
- **Extract structure, not prose.** Distill to key metrics, relationships, sequences, and takeaways.
- **Hero metric first.** Lead with the single most striking number or insight.
- **3–4 supporting stats.** Frame the story with secondary metrics.
- **Include interactivity** where the content supports it — clickable elements, hover states, `sendPrompt()` for drill-downs.

#### Design tokens
- Use CSS variables for all colors (auto dark mode)
- Font sizes: 11–16px range only
- Two weights: 400 regular, 500 bold
- Borders: 0.5px solid var(--color-border-tertiary)
- Border-radius: var(--border-radius-md) for elements, var(--border-radius-lg) for cards
- Load Chart.js from CDN: `https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js`

### Step 5: Respond

Output the visual inline via `show_widget`. Add brief commentary connecting the visual to the user's context. Keep it to 2–3 sentences max.

## What NOT to do

- Do NOT respond with a plain text summary
- Do NOT publish to HeyGenverse — that's what "ver it" is for
- Do NOT use Excalidraw — that's what "diagram it" is for
- Do NOT generate image files — that's what "nano this" is for
- Do NOT skip reading `visualize:read_me` before creating the widget
- Do NOT put explanatory prose inside the HTML — text goes in your response

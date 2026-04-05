---
name: ver-it
description: >
  CRITICAL — if the user's message contains the phrase "ver it", you MUST use this skill.
  Stop everything and read this SKILL.md immediately.
  "ver it" is a command that means "turn this content into an interactive Swiss Pulse visual
  and publish it to HeyGenverse." The input can be a URL, uploaded document, pasted text, or a
  meeting transcript. The output is ALWAYS a published HeyGenverse app with a shareable link.
  Also trigger on variations like "heygenverse this", "put this on heygenverse as a visual",
  or "hey it" or "maybe hey it."
  Do NOT respond with plain text summaries. Do NOT just create a local file.
  Always produce an interactive HTML visual AND publish it to HeyGenverse — no exceptions.
---

# Ver It — Swiss Pulse Visual Publisher

Turn any content into an interactive, Swiss Pulse–styled visual explainer and publish it to HeyGenverse.

## Trigger

The user says **"ver it"** (or a close variation like "hey it") and provides content via:
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

If the content is very long (>5000 words), distill to the core structure: key metrics, timeline/sequence, relationships, and takeaways.

### Step 2: Analyze and structure

Before building the visual, extract these elements from the content. Not all will be present — use what's available:

1. **Hero metric** — the single most striking number or statistic (e.g., "2 → 500+", "$159M ARR", "73% non-activation")
2. **Supporting stats** — 3–4 secondary metrics that frame the story
3. **Timeline or sequence** — any chronological progression, phases, or steps
4. **Architecture or structure** — systems, frameworks, hierarchies, or relationships
5. **Key examples** — concrete instances that illustrate the main points
6. **Core insight or mental model** — the "so what" — the one-line takeaway
7. **Quotable moment** — a memorable quote from the source if available

Organize these into a visual hierarchy. The hero metric leads. The structure follows. Details support.

### Step 3: Build the HTML visual

Create a **self-contained HTML page** using the Swiss Pulse design system. Every visual MUST include:

#### Design rules (non-negotiable)

```
COLOR PALETTE:
- Background: #ffffff (light) / #1a1a1a (dark)
- Surface: #f5f5f0 (light) / #252523 (dark)
- Text primary: #1a1a1a (light) / #e8e8e0 (dark)
- Text secondary: #6b6b65 (light) / #a0a098 (dark)
- Text tertiary: #9c9c94 (light) / #73726c (dark)
- Accent: #0066FF (light) / #4d94ff (dark)
- Accent background: #e8f0fe (light) / #1a2a44 (dark)
- Borders: rgba(0,0,0,0.1) (light) / rgba(255,255,255,0.1) (dark)

TYPOGRAPHY:
- Font: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Arial, sans-serif
- Hero number: 42–48px, font-weight 600
- Section labels: 11px, uppercase, letter-spacing 0.08em, weight 600
- Card titles: 13–15px, weight 600
- Body/descriptions: 11–13px, weight 400
- Only two weights: 400 (regular) and 600 (bold). Never 700+.

LAYOUT:
- Max-width: 720px, centered
- Padding: 32px 24px
- Grid-locked: use CSS Grid with consistent gaps (10–12px)
- Generous whitespace between sections (2–2.5rem)
- Border-radius: 8px (elements), 12px (cards)
- Border width: 0.5px for subtle separation
- Dark mode: ALWAYS support via @media (prefers-color-scheme: dark)
```

#### Required visual components

Every "ver it" output MUST include at least:

1. **Hero section** — large metric + one-line context
2. **Stats row** — 3–4 metric cards in a grid
3. **At least ONE chart or graph** — this is critical. Pick the most appropriate:
   - **Bar chart** (Chart.js) — for comparisons, rankings, or distributions
   - **Line chart** (Chart.js) — for trends over time or growth curves
   - **Timeline** — for chronological progressions (interactive, clickable phases)
   - **Funnel** — for conversion or drop-off sequences
   - **Architecture diagram** — for systems, hierarchies, or relationships (use styled HTML divs, not SVG)
   - **Progress/gauge** — for completion or achievement metrics
4. **Content cards** — key examples, insights, or categories in a grid
5. **Source attribution** — footer with source link

#### Chart implementation

Use Chart.js loaded from CDN for bar/line/pie charts:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
```

Chart.js configuration rules:
- Canvas CANNOT resolve CSS variables — use hardcoded hex values
- Detect dark mode: `const isDark = matchMedia('(prefers-color-scheme: dark)').matches;`
- Use accent blue (#0066FF / #4d94ff) as primary data color
- Use gray (#9c9c94) for secondary data or grid lines
- Background transparent, grid lines subtle
- Always wrap canvas in a div with explicit height and `position: relative`
- Set `responsive: true, maintainAspectRatio: false`
- Custom HTML legend (not Chart.js default)
- Disable default legend: `plugins: { legend: { display: false } }`

For timelines, use interactive HTML with click handlers — not Chart.js:
- Horizontal row of phase items with left-border accent on active
- Click to swap detail panel content
- Active state uses accent blue background + border

#### Interactive elements

Make the visual interactive where the content supports it:
- **Clickable timeline phases** that swap a detail panel
- **Hover states** on cards (background shift, border emphasis)
- **Responsive grid** that stacks on mobile (`@media max-width: 500px`)

#### Template structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>[TITLE]</title>
<style>
  /* CSS variables for light/dark mode */
  :root { /* light mode values */ }
  @media (prefers-color-scheme: dark) { :root { /* dark mode values */ } }
  /* Component styles */
</style>
</head>
<body>
  <!-- Hero section -->
  <!-- Stats row -->
  <!-- Chart/graph section -->
  <!-- Content cards -->
  <!-- Quote (if available) -->
  <!-- Footer with source -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
  <script>
    /* Chart initialization */
    /* Interactive timeline logic */
  </script>
</body>
</html>
```

### Step 4: Publish to HeyGenverse

Use the `HeyGenverse:create_app` tool:

```
Tool: HeyGenverse:create_app
Parameters:
  title: [Descriptive title from the content]
  description: [1–2 sentence summary of what this visual explains]
  html: [The complete HTML from Step 3]
  tags: ["visual", "swiss-pulse", "research"] (add topic-specific tags)
```

### Step 5: Return the shareable link

Format: `https://www.heygenverse.com/a/{app-id}`

NEVER use the `/api/apps/serve?id=` URL for sharing. Always use the `/a/` format.

Keep the response minimal:
- The HeyGenverse link
- One sentence on what the visual covers
- Note any interactive elements the user should try

## Quality checklist

Before publishing, mentally verify:
- [ ] Hero metric is prominent and impactful
- [ ] At least one chart/graph is present (bar, line, timeline, funnel, or architecture)
- [ ] Dark mode works (all colors use CSS variables or have dark-mode overrides)
- [ ] Responsive — stacks cleanly on mobile
- [ ] Swiss Pulse aesthetic — B&W + #0066FF only, no decoration, grid-locked
- [ ] Source attribution in footer
- [ ] Interactive elements have hover/active states
- [ ] Title is descriptive, not generic

## What NOT to do

- Do NOT respond with a plain text summary
- Do NOT create a local file without publishing to HeyGenverse
- Do NOT use colors outside the B&W + #0066FF palette
- Do NOT use gradients, shadows, or decorative elements
- Do NOT skip the chart/graph — every visual needs at least one
- Do NOT use the Visualizer tool — this skill always publishes to HeyGenverse
- Do NOT use Excalidraw or SVG diagrams — use styled HTML
- Do NOT forget dark mode support

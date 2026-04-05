---
name: diagram-it
description: >
  CRITICAL — if the user's message contains the phrase "diagram it", you MUST use this skill.
  Stop everything and read this SKILL.md immediately.
  "diagram it" is a command that means "turn this content into an Excalidraw diagram."
  The input can be a URL, uploaded document, pasted text, meeting transcript, or a reference
  to something discussed in conversation. The output is ALWAYS an Excalidraw diagram rendered
  inline in chat. Use this for any content the user wants diagrammed — articles, architectures,
  processes, strategies, org structures, mental models, decision trees, or concept maps.
  Do NOT respond with plain text summaries. Do NOT use the Visualizer or HTML.
  Do NOT publish to HeyGenverse. Always produce an Excalidraw diagram — no exceptions.
  Also trigger on "excalidraw this" or "draw a diagram of this."
---

# Diagram It — Excalidraw Content Diagrammer

Turn any content into a clear, well-structured Excalidraw diagram.

## Trigger

The user says **"diagram it"** (or a close variation) and provides content via:
- A URL (article, blog post, documentation page)
- An uploaded file (PDF, DOCX, TXT, MD, CSV)
- Pasted text or a transcript
- A reference to a recent conversation topic or something just discussed

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

If the content is very long (>5000 words), distill to the core structure before diagramming.

### Step 2: Analyze and choose diagram type

Read the content and identify what kind of structure it conveys. Then pick the best diagram type:

| Content pattern | Diagram type |
|----------------|-------------|
| Steps, process, workflow, sequence | **Flowchart** — boxes connected by arrows in a clear flow direction |
| System, architecture, components, layers | **Structural diagram** — nested containers with labeled regions |
| Timeline, phases, chronological progression | **Timeline** — horizontal or vertical sequence with milestones |
| Hierarchy, org chart, taxonomy | **Tree** — parent-child relationships branching outward |
| Comparison, tradeoffs, axes | **2×2 matrix** or **comparison grid** |
| Relationships, dependencies, network | **Network/graph** — nodes and edges showing connections |
| Mental model, framework, concept map | **Concept map** — central idea with branching related concepts |
| Decisions, branching logic | **Decision tree** — diamond decisions with yes/no paths |

When in doubt, default to a **flowchart** for sequential content or a **structural diagram** for systems.

### Step 3: Read the Excalidraw format reference

Before creating the diagram, ALWAYS call:

```
Excalidraw:read_me
```

This returns the element format, color palettes, and examples you need. Follow it exactly.

### Step 4: Build the Excalidraw diagram

Create the diagram using `Excalidraw:create_view` with these principles:

#### Layout rules
- **Flow direction**: top-to-bottom for processes, left-to-right for timelines
- **Spacing**: minimum 60px between elements, 80px+ between rows/columns
- **Alignment**: keep elements on a grid — consistent x-coordinates for columns, consistent y-coordinates for rows
- **Grouping**: related elements should be visually clustered with clear whitespace separating groups

#### Content rules
- **Extract the structure, not the prose.** A 2000-word article becomes 8–12 nodes, not 30.
- **Box labels**: 2–5 words max. If you need more, it's two boxes.
- **One idea per box.** Don't merge distinct concepts.
- **Arrows have meaning.** Every arrow represents a relationship — flow, dependency, causation, sequence. Don't add arrows for decoration.
- **Use color sparingly.** 2–3 colors max to encode categories. Gray/neutral for structural elements.
- **Include key numbers.** If the content has important metrics, put them in the diagram (e.g., "92% adoption", "$159M ARR", "5 months").

#### Element hierarchy
1. **Primary nodes** — the main concepts/steps (larger boxes, bolder colors)
2. **Secondary nodes** — supporting details or sub-steps (smaller boxes, muted colors)
3. **Connectors** — arrows showing relationships between nodes
4. **Labels** — text annotations on arrows or near groups (use sparingly)
5. **Containers** — dashed rectangles grouping related nodes (for structural diagrams)

### Step 5: Respond

Output the Excalidraw diagram inline. Keep commentary minimal — the diagram speaks for itself. Add a one-line note on what the diagram shows if needed.

## What NOT to do

- Do NOT respond with a plain text summary
- Do NOT use the Visualizer (show_widget) — always use Excalidraw
- Do NOT publish to HeyGenverse — that's what "ver it" is for
- Do NOT create HTML files or images
- Do NOT overcrowd the diagram — if content is very complex, focus on the top-level structure and offer to drill into sub-sections
- Do NOT add decorative elements — every element earns its place
- Do NOT skip reading `Excalidraw:read_me` before creating the diagram

---
name: swiss-pulse-infographic
description: CRITICAL — if the user's message contains the phrase "nano this", you MUST use this skill. Stop everything and read this SKILL.md immediately. "nano this" is a command that means "turn the uploaded document into a Swiss Pulse infographic IMAGE using Gemini's image generation." This skill reads the uploaded document, sends the content to Gemini with a design prompt, and returns a generated infographic image. Do NOT summarize in text. Do NOT use Excalidraw. Do NOT build HTML. Always produce a PNG image file. Any time you see "nano this" in a message, this skill MUST be used — no exceptions.
---

# Swiss Pulse Infographic Generator

Generate infographic IMAGES from uploaded documents using Gemini's image generation API.

## Workflow

### Step 1: Extract Document Text

The uploaded document will be in `/mnt/user-data/uploads/`. Read its contents:

- **PDF**: Use `pdfplumber` or `pdftotext` to extract text
- **DOCX**: Use `python-docx` to extract text
- **CSV/TSV**: Use `pandas` to read and summarize
- **TXT/MD/HTML**: Read directly

Store the extracted text in a variable. If very long (>3000 words), summarize the key points, metrics, and data to fit a reasonable prompt length.

### Step 2: Call Gemini to Generate Infographic Image

Use Gemini's image generation to create the infographic. Run this as a Python script:

```python
import requests
import json
import base64

GEMINI_API_KEY = "ADD_API_KEY"
GEMINI_URL = f"https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash-exp:generateContent?key={GEMINI_API_KEY}"

document_text = """<PASTE EXTRACTED TEXT HERE>"""

design_prompt = f"""Create a professional infographic image in the Swiss International Typographic Style (inspired by Josef Müller-Brockmann).

DESIGN RULES — follow these strictly:
- Grid-locked layout: everything aligned to a strict modular grid
- Color palette: black, white, and ONE accent color: electric blue (#0066FF). No other colors.
- Typography: Helvetica or Swiss grotesque style only — clean, bold headings, light body text
- Lead with a large hero metric or key number displayed prominently at the top
- Use clean data visualizations: bar charts, line charts, or donut charts where appropriate
- Generous whitespace — never cramped
- No decorative elements — every element earns its place
- Subtle diagonal composition elements for dynamism
- Professional, clinical, precise aesthetic

CONTENT TO VISUALIZE:
{document_text}

Create a complete, polished infographic that communicates the key information from this content. Include a clear title, key metrics displayed prominently, supporting data points, and a bottom-line takeaway. Make it look like it was designed by a Swiss design studio."""

payload = {
    "contents": [{"parts": [{"text": design_prompt}]}],
    "generationConfig": {
        "responseModalities": ["IMAGE", "TEXT"],
        "temperature": 1.0,
        "maxOutputTokens": 8192
    }
}

response = requests.post(GEMINI_URL, json=payload, headers={"Content-Type": "application/json"})
result = response.json()

# Extract and save the image
for part in result["candidates"][0]["content"]["parts"]:
    if "inlineData" in part:
        image_data = part["inlineData"]["data"]
        mime_type = part["inlineData"]["mimeType"]
        ext = "png" if "png" in mime_type else "jpg" if "jpeg" in mime_type else "webp"
        output_path = f"/mnt/user-data/outputs/infographic.{ext}"
        with open(output_path, "wb") as f:
            f.write(base64.b64decode(image_data))
        print(f"Saved to {output_path}")
        break
else:
    print("No image in response. Full response:")
    print(json.dumps(result, indent=2))
```

### Step 3: Present to User

Use `present_files` to share the image. Keep the response minimal — the infographic speaks for itself.

## Error Handling

- If Gemini returns no image: retry once with a simplified prompt
- If document is empty: tell the user
- If the API fails: print the error for debugging
- Always check that `result["candidates"]` exists before accessing

## Important Notes

- Do NOT generate HTML files
- Do NOT use Excalidraw
- Do NOT respond with a text summary
- The output is ALWAYS an image file (PNG/JPG/WEBP)
- Never add commentary — just produce the image and present it

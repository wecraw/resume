# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repo holds Will Crawford's resume in several formats:
- `WillCrawford_Resume_2026.tex` — the canonical source (LaTeX)
- `WillCrawford_Resume_2026.pdf` — the compiled output, checked in alongside the source
- `README.md` — a Markdown mirror of the resume content
- `index.html` / `resume.html` — a static web version of the resume (see below)

When editing resume content, update the `.tex` file, `README.md`, **and** the HTML pages to keep them in sync.

## Building

Compile the LaTeX source to PDF with:

```bash
pdflatex WillCrawford_Resume_2026.tex
```

This requires a LaTeX distribution (e.g. MacTeX or TeX Live). After compiling, the updated `.pdf` should be committed alongside any `.tex` changes.

## LaTeX Template Structure

The `.tex` file is based on [Jake Gutierrez's resume template](https://github.com/sb2nov/resume). Key custom commands defined in the preamble:

- `\resumeSubheading{title}{date}{role}{date}` — job/education entry with two-line header
- `\resumeSubSubheading{role}{date}` — continuation role under same employer (no repeated company name)
- `\resumeProjectHeading{title+tech}{date}` — project entry
- `\resumeItem{text}` — single bullet point
- `\resumeSubHeadingListStart` / `\resumeSubHeadingListEnd` — wraps groups of subheadings
- `\resumeItemListStart` / `\resumeItemListEnd` — wraps bullet lists under a subheading

Sections follow the order: Experience → Projects → Education → Technical Skills.

## Static Web Version (`index.html` + `resume.html`)

Two self-contained HTML files (inline `<style>`, no build step or dependencies), mirroring izs.me's structure:

- `index.html` — a minimalist landing page: contact, a condensed Work list (dates + employer + role, no bullets), a few "Stuff I Do" links, and a short School blurb. Ends with `[longer]` (→ `resume.html`) and `[pdf]` links.
- `resume.html` — the full résumé (every role with accomplishment bullets, Projects, Education, Technical Skills). Ends with a `[shorter]` link back to `index.html`.

Both emulate the "back-to-markdown" aesthetic of [izs.me](https://izs.me/): semantic HTML rendered in a monospace column so it *looks* like Markdown source — CSS generated content adds the `#`/`##` heading prefixes (teal), `*` list bullets (amber), `**`/`_` emphasis markers, `---` rules, and `[ ]` brackets around links. The `<style>` block is identical in both files; if you change it, change it in both.

When updating content: `index.html` is the condensed view and `resume.html` is the full one (kept in sync with `README.md`). The markdown-style markers are purely CSS (`::before`/`::after`) — write clean semantic HTML (`<h2>`, `<ul>`, `<strong>`, `<a>`) and let the stylesheet decorate it; do not hand-type the `#`, `*`, or `[ ]` characters.

## Deploying HTML Changes

After any edit to the HTML files, always:

1. **Deploy to Amplify** (app ID `d107gyx2unpg5z`, branch `main`) via manual zip upload:
   ```bash
   zip -r deploy.zip index.html resume/index.html CNAME
   UPLOAD=$(aws amplify create-deployment --app-id d107gyx2unpg5z --branch-name main --output json)
   JOB_ID=$(echo "$UPLOAD" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['jobId'])")
   UPLOAD_URL=$(echo "$UPLOAD" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['zipUploadUrl'])")
   curl -s -X PUT -H "Content-Type: application/zip" --data-binary @deploy.zip "$UPLOAD_URL"
   aws amplify start-deployment --app-id d107gyx2unpg5z --branch-name main --job-id "$JOB_ID"
   ```
   Wait for the job to reach `SUCCEED` status before reporting done.

2. **Commit and push** the changes to git:
   ```bash
   git add index.html resume/index.html
   git commit -m "<short description>"
   git push
   ```

The two HTML files are `index.html` (root) and `resume/index.html` (full resume) — always include both in the zip even if only one changed.

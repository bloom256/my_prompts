# Repository Guidelines

## Project Aim
This repo is a single-source notebook for high-value AI prompts. Every entry should help future-you (or teammates) drop a ready-to-use instruction into a model without hunting through chat history. Keep wording actionable, note any required context, and keep sections skimmable.

## File Layout
- `PROMPTS.md` – the only required file. Organize with `## Category` headings (e.g., `## Ideation`, `## Coding Help`) and `### Scenario — Goal` subheadings. Each scenario contains:
  - A one-line intent statement.
  - The exact prompt inside a ```text block.
  - Optional bullets for usage notes, required placeholders, or example outputs.
- `ARCHIVE.md` – optional parking lot for retired or experimental prompts so the main file stays tight.
- `assets/` – optional folder for redacted screenshots or transcripts referenced from `PROMPTS.md`.

## Editing Workflow
1. Draft new prompts at the bottom of the relevant category, then move them up once vetted.
2. Run `markdownlint PROMPTS.md` (if installed) or at least preview the file to ensure headings and anchors render correctly.
3. When committing, describe the affected category (`docs(prompts): add design critique flow`) and mention whether you manually trialed the prompt.

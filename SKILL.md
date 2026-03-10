---
name: tts-tool
description: Text-to-speech workflow for turning user-provided text or markdown into pleasant `.opus` audio with `chunktts`. Use when text contains symbols, URLs, formulas, markdown, or technical notation that should be rewritten for natural speech before TTS.
---

# TTS Tool

Follow this workflow exactly to turn text into pleasant speech audio.

## Resolve Input

This skill is text-only.

- If the user provides a text or markdown file, read that file.
- If the user pastes text directly, use that text as the source.
- Do not use OCR or PDF extraction.

## Process TTS Input

Always preprocess the text yourself before running `chunktts`.

- Do not use custom scripts or programmatic pipelines to generate the cleaned TTS
  input text. Read the source directly and rewrite it manually for speech.

### Installation

- Check with `command -v chunktts`.
- If missing, read [references/chunktts-install.md](references/chunktts-install.md) and attempt installation.
- Retry `command -v chunktts` after installation. If still missing, stop and report.

### Execution

- Request unrestricted network/escalated execution directly in the tool call.
  Do not run a sandboxed `chunktts` attempt as a probe.
- Do not inspect environment variables, shell profiles, or filesystem files to discover API keys.
  If `chunktts` reports an auth/config failure, report the error and ask the user to configure
  `DEEPINFRA_API_KEY` or `config.json`, then retry.

### Usage

- `chunktts INPUT.txt OUTPUT.opus`

## Rewrite for Speech

Rewrite for natural speech, not visual fidelity.

- Remove markdown syntax such as `#`, `##`, `*`, `_`, `` ` ``, `>`, code fences,
  table pipes, and horizontal rules.
- Keep visible text, not formatting.
- Turn bullet and numbered list items into normal spoken sentences.
- For markdown links, keep the visible link text and drop the raw URL target,
  unless the URL itself is the important content.
- Rewrite raw URLs into natural spoken form. Do not leave `http://`, `https://`,
  query strings, fragments, or symbol-heavy path syntax unless they must be spoken.
- Rewrite email addresses into forms like `name at domain dot com`.
- Remove LaTeX delimiters like `$`, `$$`, `\(`, `\)`, `\[`, `\]`.
- Rewrite common TeX or math notation into words, such as:
  - `\alpha` -> `alpha`
  - `\beta` -> `beta`
  - `=` -> `equals`
  - `+` -> `plus`
  - `-` -> `minus`
  - `/` -> `over`
  - `^2` -> `squared`
  - `^3` -> `cubed`
- Rewrite file paths, flags, and technical identifiers into more speakable text.
  Avoid long runs of punctuation or symbols.
- Drop decorative content that has no spoken value.

## Chunking

You decide where `<bk>` goes.

- Chunk by spoken thought units, not document structure.
- Use whole paragraphs and list items as the default units.
- Headings are optional source hints, not mandatory spoken content.
- Drop generic headings that do not improve the listening experience.
- Keep a heading only if it adds real spoken meaning and sounds natural aloud.
- Prefer shorter spoken sentences. If a sentence sounds dense or awkward, rewrite
  it into two shorter natural sentences before chunking.
- If a paragraph or list item is too long, split it first at sentence boundaries.
- Split inside a sentence only as a last resort.
- Keep chunks conservative for Kokoro-82M:
  - target about `260-420` characters
  - preferred upper bound `520`
  - hard ceiling `620`
- Never emit empty chunks.
- Collapse repeated `<bk>` markers.

## Produce Output

- Write the cleaned text with `<bk>` markers to a temporary input file.
- Run `chunktts` with that file and an agent-managed `.opus` output path.
- Tell the user the exact location of the generated `.opus` file.

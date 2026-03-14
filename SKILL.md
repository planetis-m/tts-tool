---
name: tts-tool
description: Text-to-speech workflow for turning user-provided text or markdown into pleasant `.opus` audio. Use when text contains symbols, URLs, formulas, markdown, or technical notation that should be rewritten for natural speech before TTS.
---

# TTS Tool

Follow this workflow exactly to turn text into natural-sounding `.opus` speech.

Do not add verification steps unless the user explicitly asks.

## Process TTS Input

Always rewrite the input yourself before running `chunktts`.

- Do not use scripts or programmatic pipelines to generate the cleaned TTS input.
  Read the source directly and rewrite it manually for speech.

### Installation

- Check for `chunktts` with `command -v chunktts`.
- If it is missing, read [references/chunktts-install.md](references/chunktts-install.md) and attempt installation.
- Check again after installation. If it is still missing, stop and report the failure.

### Execution

- Request unrestricted network or escalated execution directly in the tool call.
  Do not run a sandboxed `chunktts` probe first.
- Do not inspect environment variables, shell profiles, or filesystem files to discover API keys.
  If `chunktts` reports an auth or config failure, report the error and ask the user to configure
  `DEEPINFRA_API_KEY` or `config.json`, then retry.

### Usage

- Command form: `chunktts INPUT.txt OUTPUT.opus`

## Rewrite for Speech

Optimize for natural speech, not visual fidelity.

- Remove markdown syntax such as `#`, `##`, `*`, `_`, `` ` ``, `>`, code fences,
  table pipes, and horizontal rules, but keep the visible text.
- Turn bullet points and numbered items into normal spoken sentences.
- For markdown links, keep the visible link text and drop the raw URL target unless the URL itself matters.
- Rewrite raw URLs into natural spoken form. Do not leave `http://`, `https://`,
  query strings, fragments, or punctuation-heavy path syntax unless they must be spoken.
- Rewrite email addresses into forms like `name at domain dot com`.
- Remove LaTeX delimiters such as `$`, `$$`, `\(`, `\)`, `\[`, and `\]`.
- Rewrite common TeX or math notation into spoken words, such as:
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
- Drop decorative or purely visual content that adds no spoken value.

## Chunking

You decide where `<bk>` goes.

- Chunk by spoken thought units, not by document structure.
- Use whole paragraphs and list items as the default chunking units.
- Treat headings as optional hints, not mandatory spoken content.
- Drop generic headings that do not improve the listening experience.
- Keep a heading only if it adds real spoken meaning and sounds natural aloud.
- Prefer shorter spoken sentences. If one sounds dense or awkward, rewrite it into
  two shorter natural sentences before chunking.
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
- Run `chunktts` against that file and an agent-managed `.opus` output path.
- Tell the user the exact location of the generated `.opus` file.

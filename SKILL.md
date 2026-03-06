---
name: tts-assistant
description: Text-to-speech workflow for turning user-provided text or markdown into pleasant `.opus` audio with `chunktts`. Use when text contains symbols, URLs, formulas, markdown, or technical notation that should be rewritten for natural speech before TTS.
---

# TTS Assistant

Follow this workflow exactly to turn text into pleasant speech audio.

## Resolve Input

This skill is text-only.

- If the user provides a text or markdown file, read that file.
- If the user pastes text directly, use that text as the source.
- Do not use OCR or PDF extraction.

## Process TTS Input

Always preprocess the text yourself before running `chunktts`.

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

Read and apply [references/commands.md](references/commands.md) before writing the
temporary input file.

Global rules:

- Rewrite for natural speech, not visual fidelity.
- Remove or rewrite symbols instead of leaving them literal.
- Convert markdown, URLs, LaTeX, and technical notation into speech-friendly text.
- Decide the best places to insert `<break>`.
- Keep chunks conservative for Kokoro-82M:
  - target about `220-320` characters
  - preferred upper bound `360`
  - hard ceiling `420`
- Never emit empty chunks.
- Collapse repeated `<break>` markers.

## Produce Output

- Write the cleaned text with `<break>` markers to a temporary input file.
- Run `chunktts` with that file and an agent-managed `.opus` output path.
- Tell the user the exact location of the generated `.opus` file.

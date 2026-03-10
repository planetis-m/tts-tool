# TTS Tool Skill

`tts-tool` is an installable Agent Skill for turning text or markdown into
pleasant speech audio with `chunktts`.

## Features

It provides one workflow:
- rewrites symbol-heavy or markdown-heavy text into natural spoken text
- inserts `<bk>` markers at speech-friendly boundaries
- runs `chunktts`
- returns one final `.opus` file and its location

This skill is designed for content that would otherwise sound bad in TTS, such
as raw URLs, markdown syntax, LaTeX, file paths, shell commands, and technical
notation.

## Requirements

- **`chunktts`**: Required to generate the final `.opus` file.
- **DeepInfra API Key**: Required by `chunktts`.
  - Set it via `DEEPINFRA_API_KEY` (recommended).
  - Or provide it via `config.json` next to the `chunktts` executable.

## Installation

### Using Codex
Codex recommends installing non-built-in skills using the `$skill-installer`.
Prompt Codex with:

```text
$skill-installer install the skill from repo planetis-m/tts-tool with path .
```

### Manual Install
Clone directly into your agent's scanned skills path:

```bash
git clone https://github.com/planetis-m/tts-tool.git ~/.agents/skills/tts-tool
```

## Usage Examples

Invoke the skill explicitly using `$tts-tool` in your prompts:

```text
Use $tts-tool to turn this markdown into a pleasant spoken .opus file.
```

```text
Use $tts-tool on notes.md and generate the final audio.
```

# TTS Rewrite Rules

Apply these rules before writing the temporary input file for `chunktts`.

## Goal

Turn the source into pleasant spoken text, not faithful visual markup.

## Rewrite Rules

- Remove markdown syntax such as `#`, `##`, `*`, `_`, `` ` ``, `>`, code fences,
  table pipes, and horizontal rules.
- Keep headings as plain spoken text, usually as their own short section.
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

## Chunking Rules

You decide where `<break>` goes.

- Prefer natural speech boundaries:
  - headings
  - paragraph breaks
  - list items
  - sentence endings
- If a section is long, split it again at sentence boundaries.
- If it is still too long, split at commas, semicolons, or other clause breaks.
- Keep chunks conservative for Kokoro-82M:
  - target about `220-320` characters
  - preferred upper bound `360`
  - hard ceiling `420`
- Never emit empty chunks.
- Collapse repeated `<break>` markers.

## Execution Rules

- Write the cleaned text with `<break>` markers to a temporary input file.
- Run `chunktts INPUT.txt OUTPUT.opus`.
- Report the exact path to the final `.opus` file.

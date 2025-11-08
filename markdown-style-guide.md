# Markdown Style Guide

---

## Core Principles

-   **Clarity over decoration** — readability is the priority.
-   **Minimal markup** — only use syntax that adds meaning.
-   **Compact layout** — avoid wasted blank lines.
-   **Consistent hierarchy** — clear, regular heading rhythm.
-   **Portable output** — renders well in GitHub, Notion, docs.

---

## Structure

-   Use only `#`, `##`, and `###` for headings; no deeper nesting.
-   Start with one top-level `# Title`.
-   Separate major sections with `---` (horizontal rule).
-   Break up long paragraphs (~80 characters) for easier reading.

Example:

# Title

## Section

Content here.

---

## Another Section

More content.

---

## Spacing

-   1 blank line after headings
-   0–1 blank lines between paragraphs (for clarity)
-   No leading/trailing blank lines in files
-   No double spaces at line ends

---

## Lists

-   Use `-` for unordered, `1.` for ordered
-   Indent sub-lists with max 2 spaces
-   List items should be single-line where possible

Example:

-   Simple
-   Consistent
    -   Nested

---

## Code

-   Inline code: `` `likeThis()` ``
-   Code blocks: use triple backticks with language (e.g. `js`, `tsx`, `bash`)
-   No extra blank lines inside code blocks

Example:

```js
function Component() {
	return <div>Hello</div>;
}
```

---

## Emphasis

-   Use **bold** for important ideas
-   Use _italic_ for tone or emphasis
-   Avoid ~~strikethrough~~ unless showing modification

---

## Links

-   Always use `[text](url)` (avoid raw URLs unless needed)
-   Prefer relative links within a repo (`./path/file.md`)
-   Optionally, use footnote style for very long links

---

## Quotes

-   Use `>` only for actual quotes, not emphasis
-   Keep each quote short, one paragraph max

Example:

> Clarity is better than cleverness.

---

## Tables

-   Use sparingly; prefer lists unless content is truly tabular
-   Columns should be left-aligned

Example:

| Key | Value |
| :-- | :---- |
| Foo | Bar   |

---

## Inline Rules

-   No HTML except badges/special formatting
-   No embedded images unless truly needed
-   No decorative emojis or excessive bold text
-   Avoid blending Markdown and HTML

---

## Filenames

-   All lowercase
-   Use `-` for separators
-   Make names meaningful and scoped (e.g. `frontend-guide.md`, not `doc1.md`)

---

## Metadata

-   No frontmatter unless required by static generators
-   If used, keep metadata block minimal

Example:

---

title: React Frontend Guide
description: Minimal patterns and principles.

---

---

## Checklist Format

-   `[ ]` for to-dos
-   `[x]` for done items
-   One item per line, keep checklists short

---

## Summary Style

End files with a short summary block or takeaway.

---

**Summary:**  
Keep Markdown clean, consistent, and minimal.

---

_Readable > Pretty.  
Consistent > Fancy.  
Every `.md` file should be glanceable in ~10 seconds._

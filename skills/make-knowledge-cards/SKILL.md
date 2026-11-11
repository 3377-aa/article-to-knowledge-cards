---
name: make-knowledge-cards
description: Convert a pasted article or a local Markdown/TXT file into 5-8 knowledge cards. Use when the user provides article text or a .md/.txt file path and asks for knowledge cards, study cards, or revision notes. Each card covers exactly one knowledge point with a title, core knowledge, a plain-language explanation, and an example or self-test question.
---

# Make Knowledge Cards

## Overview

Turn one source document (pasted text, or a local `.md`/`.txt` file) into 5-8 knowledge cards for study and review. Extract only what the source actually says — never invent facts, and never pad the deck to hit a card count.

## Supported Input

- Text pasted directly into the conversation.
- A local file path ending in `.md` or `.txt` (plain text, UTF-8).

**Out of scope** (do not attempt, explain the limitation and stop):
- Web page URLs / web scraping
- PDF, DOCX, HTML, EPUB, or any binary/converted format
- Anki decks or spaced-repetition export
- Any graphical interface

If the user provides an unsupported input type, state what is supported and ask for a `.md`/`.txt` file or pasted text.

## Workflow

1. **Acquire the source text.**
   - Pasted text: use it directly.
   - File path: read the file. If the file does not exist or the extension is not `.md`/`.txt`, report the problem and stop. Do not guess at file contents.

2. **Read the full text before extracting anything.** Skim once for structure (sections, headings, lists, repeated ideas), then identify candidate knowledge points.

3. **Select knowledge points.** Apply these rules strictly:
   - One card = one knowledge point. Split compound ideas; merge nothing across cards.
   - Rank candidates by importance: definitions, mechanisms, procedures, conditions, distinctions, and concrete facts the source emphasizes. Drop trivia, transitions, and filler.
   - Deduplicate: repeated or paraphrased ideas appear once. Restated content from intro/summary sections counts as duplication.
   - Target 5-8 cards. If the source genuinely supports fewer, output fewer. **Never pad** with vague, generic, or weakly supported cards, and never add information not present in the source.
   - **When the point count falls short, prefer fewer cards over merged cards.** A card that contains two claims (two mechanisms, two consequences, two separate rules) is a defect, not a compromise. Split it or drop the weaker half — never merge to reach the target count.

4. **Write the cards.**
   - Card content must be written in the same language as the source text.
   - Quotes, numbers, terms, and names must match the source exactly. If unsure whether the source states something, leave it out.
   - Each card must have all four fields (see format below). Vary the last field across the deck: use a concrete example from the source for some cards and a self-test question for others.

5. **Review the deck once before presenting.** Check every item and fix before output:
   - No card duplicates another.
   - No card contains a claim absent from the source.
   - Each card carries exactly one claim (see Step 3 on merged cards).
   - Each card is self-contained, understandable without the source.
   - Labels and content use the same language (see Card Format).
   - The card count is honest.

## Card Format

Output the deck as Markdown, one card per section, numbered.

**Label language follows the source language.** Translate the four field labels into the source language and never mix languages — a Chinese article gets Chinese labels and Chinese content, an English article gets `Card` / `Key point` / `Explanation` / `Example or self-test`, and so on.

Chinese source:

```markdown
## 卡片 1：<标题>

- **核心知识**：<一句话陈述该知识点，必须来自原文>
- **简明解释**：<用通俗语言解释，2-4 句>
- **例子/自测**：<原文中的例子，或一个可自查答案的自测问题>
```

English source:

```markdown
## Card 1: <Title>

- **Key point**: <one sentence, taken from the source>
- **Explanation**: <plain-language explanation, 2-4 sentences>
- **Example or self-test**: <an example from the source, or a self-test question with a checkable answer>
```

Title rules: short, concrete, and different from every other title — no generic "XX 简介" / "Introduction to XX" style titles repeated across cards.

Self-test question rules: questions must be answerable from the source text alone. Open-ended speculation questions are not allowed. Where the source provides a concrete example, prefer the example over a question.

## Presentation Rules

- Present the deck in the conversation as Markdown.
- If the deck has fewer than 5 cards, say so explicitly and explain that the source does not contain enough distinct, important knowledge to justify more — this is expected behavior, not a failure.
- If the source is too short or too thin to produce even 3 meaningful cards, say so and stop instead of producing a weak deck.

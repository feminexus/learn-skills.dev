---
name: skill-craftsman
description: Reference and reviewer for crafting, refining, and auditing high-quality AI agent skills. Enforces process predictability, leading words, zero-noise output contracts, and anti-pattern diagnostics.
---

# Skill Craftsman

Provides architectural principles, domain vocabulary, and auditing workflows for designing and reviewing AI agent skills.

Root virtue: **process predictability** — enforcing consistent behavioral contracts, completion criteria, and precise domain lexicon.

---

## Trigger Conditions
* "create a skill", "write a skill", "review a skill", "audit a skill", "refine skill language".
* Reviewing `.md` files in `~/.agent/skills/` or `~/.gemini/skills/`.

---

## Domain Lexicon (Context Pointer)

Eliminate ambiguous prose and use precise domain concepts, verbs, and anti-pattern definitions.

**[View Skill Lexicon](GLOSSARY.md)**

Refer to `GLOSSARY.md` for exact definitions of terms like *Behavioral Contract*, *Context Load*, *Legwork*, *Premature Completion*, and *Router Skill*.

---

## Core Mechanics

### 1. Invocation Mechanics
- **Model-Invoked**: Omit `disable-model-invocation`. Front-load description with distinct **Trigger Branches**. Incurs **Context Load** every turn.
- **User-Invoked**: Set `disable-model-invocation: true`. Zero Context Load, but incurs **Cognitive Load** on user.
- **Router Skill**: User-invoked meta-skill routing to sub-skills.

### 2. Information Hierarchy
1. **In-skill Steps**: Sequence in `SKILL.md`. Each step terminates on a **Completion Criterion**.
2. **In-skill Reference**: Rules co-located under clear headings.
3. **External Reference**: Pushed behind a **Context Pointer** (`GLOSSARY.md`, `references/`).

### 3. Leading Words & Implicit Tooling
- **Leading Words**: Anchor complex behaviors with pretrained conceptual shortcuts (e.g., *tight*, *red*, *legwork*).
- **Implicit Tooling**: Do NOT over-specify exact system tool names (e.g., write "Read file" instead of "Use `view_file`"). The agent inherently maps intent to available tools.

### 4. High-Signal / Zero-Noise Output Contract
- **Bullet Points**: Use strict bullet points (`-`) for explanations/summaries. No continuous prose paragraphs.
- **No-Op**: Emit minimal status if no action is needed (`Clean. No action needed.`).
- **Action Completed**: Output concise execution summaries without full diffs or restating steps.
- **Non-Noise**: Exclude preamble, greetings, conversational filler, and self-congratulatory postambles.

---

## Anti-Pattern Diagnostics

Identify and eliminate: **Premature Completion**, **Duplication**, **Sediment**, **Sprawl**, **No-ops**, **Negation**, and **Output Noise**.

*(See `GLOSSARY.md` for definitions and remedies.)*

---

## Audit Workflow

1. **Ingest & Dissect**: Read target skill and analyze structure.
2. **Diagnose Failure Modes**: Check for Sediment, Sprawl, No-ops, Output Noise, Negation, and Over-specified Tooling.
3. **Refine Lexicon & Prune Prose**: Replace generic terms with precise **Domain Lexicon**, strip redundant filler, and convert verbose tool names into implicit action verbs.
4. **Enforce Hierarchy**: Ensure every step ends with an explicit **Completion Criterion**.
5. **Enforce Zero-Noise Output Contract**: Strip preamble, postamble, and conversational filler.
6. **Synthesize Changes**: Apply edits and report concise structural improvements.

---

## Validation Checklist
- [ ] Frontmatter tuned for Context vs. Cognitive load?
- [ ] Free of explicit tool-name over-specification (implicit tooling applied)?
- [ ] Stripped of conversational prose filler, preamble, and postamble?
- [ ] Steps terminate on checkable **Completion Criteria**?
- [ ] Free of Sediment, Sprawl, No-ops, Output Noise, and Negation?
- [ ] Replaced generic verbs with precise domain terms?
- [ ] Output strictly obeys High-Signal / Zero-Noise contract?

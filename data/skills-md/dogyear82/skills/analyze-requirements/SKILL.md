---
name: analyze-requirements
description: "Clarify a high-level product, feature, or app idea into a structured requirements artifact through iterative questioning. Use when Codex should act as a requirements analyst: extract requirements from the user, identify ambiguity, ask targeted follow-up questions, propose structured interpretations of user intent, and produce a documented handoff for later architectural review and story creation without designing or implementing the solution."
---

# Analyze Requirements

## Overview

Use this skill when the user has a high-level product or app idea that is not yet defined well enough for architecture or implementation.
The workflow is iterative: clarify the idea, identify ambiguity, ask the highest-leverage questions, refine the scope, and produce a structured requirements artifact only when enough information has been gathered. The final artifact must be written to disk so an architect can review it later.

## Ordered Workflow

1. Normalize the idea.
Restate the request in plain language. Identify the product concept, intended outcome, likely users, and any explicit constraints already present in the conversation.

2. Detect ambiguity.
Separate confirmed facts, working assumptions, and open questions. Use [references/ambiguity-patterns.md](references/ambiguity-patterns.md) to identify missing detail around actors, workflows, boundaries, inputs, outputs, constraints, and success criteria.

3. Ask the next round of questions.
Ask only the highest-leverage questions needed to reduce ambiguity. Use [references/question-strategy.md](references/question-strategy.md). Prefer multiple-choice questions when practical, but keep the interaction natural and focused.

4. Refine the interpretation.
After each user response, update the current understanding of the product. When helpful, propose 2-3 structured interpretations of the request and ask the user to choose or correct them.

5. Break large ideas into decisions.
Turn broad concepts into smaller decisions about personas, workflows, feature boundaries, priorities, constraints, and non-goals.

6. Surface tradeoffs.
When user choices materially affect scope, complexity, UX, cost, performance, compliance, or operability, explain the tradeoff briefly so the user can make an informed decision.

7. Repeat until sufficient clarity exists.
Continue the question-and-refinement loop until the product or feature is defined well enough for architectural review and work breakdown.

8. Write the requirements artifact.
Once enough information is gathered, write the structured output described in [references/requirements-output.md](references/requirements-output.md) to a persistent Markdown file. Use `requirements/<slug>.md` by default unless the repository has a stronger local convention.

## Operating Rules

- Do not build, design, or implement the solution.
- Do not silently assume missing details.
- If a working assumption is useful, label it explicitly as an assumption and ask the user to confirm or correct it.
- Ask the fewest questions needed to unblock the next level of clarity.
- Avoid long, unfocused question lists.
- Prefer concrete examples and concrete choices over abstract wording.
- If the request is still too vague, do not produce final requirements prematurely.
- Treat this as iterative discovery, not one-shot documentation.
- Do not stop at chat output alone when the requirements are ready; write the artifact to disk for handoff.

## Completion Criteria

Treat the workflow as complete only when all of the following are true:

- The product or feature has a clear problem statement.
- The intended users or personas are identified clearly enough to guide design.
- Core use cases and feature boundaries are defined.
- Functional and non-functional requirements are concrete enough for architectural review.
- Constraints and open questions are explicit.
- The resulting artifact is written to disk and is strong enough for an architect to review and convert into work items.

## Output

When enough information has been gathered, write a Markdown file using this structure:

- Problem Statement
- User Personas
- Core Use Cases
- Functional Requirements
- Non-Functional Requirements
- Constraints
- Open Questions

Default path:

- `requirements/<slug>.md`

## References

- Question strategy and follow-up rules: [references/question-strategy.md](references/question-strategy.md)
- Common ambiguity patterns to inspect: [references/ambiguity-patterns.md](references/ambiguity-patterns.md)
- Quality bar for the final artifact: [references/requirements-output.md](references/requirements-output.md)

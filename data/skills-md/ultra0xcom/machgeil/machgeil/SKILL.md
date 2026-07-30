---
name: machgeil
description: Take an existing result, implementation, idea, text, design, plan, prompt, or codebase and relentlessly improve it until it cannot reasonably be made significantly better. Use when the user says "mach geil", "make this geil", "improve this until it's the best version", "don't stop at good enough", "polish this hard", "upgrade this", "make it production-worthy", "elevate this", or asks to push quality on something that already exists or works.
---

# machgeil

**Mach geil.**  
Take what exists and make it substantially, noticeably better — until further meaningful improvement would be marginal, speculative, or overengineered.

Do not stop early. The Stopping Condition (below) defines when to deliver.

The user is asking for the strongest, cleanest, most robust, elegant, and intentional version you can reasonably produce.

## Core Principle

Preserve the user's exact intent and goal.  
Upgrade the execution without mercy or compromise.

The result must become noticeably:

- clearer and stronger
- more useful and robust
- more elegant and intentional
- more complete and practical
- less fragile, generic, or awkward

A machgeil result makes the user feel:  
> "Yes. That is the version I actually wanted."

## When to Use

Activate on any request to improve, refine, polish, elevate, harden, or perfect an existing artifact:

- Code: feature, refactor, bugfix, architecture, tests
- Writing: docs, prompts, messages, specs, copy
- Designs, product concepts, strategies, plans
- Existing implementations or outputs

**Do NOT use** for pure greenfield creation from scratch (use brainstorming or planning skills first).  
Use machgeil when something already exists that deserves to be made excellent.

## The Improvement Loop

Run this loop until the stopping condition is met. Never skip steps.

1. **Understand**  
   Restate the real goal + constraints. Thoroughly inspect the current artifact (files, logic, structure, tone, assumptions).

2. **Audit**  
   List concrete weaknesses across these dimensions:
   - Correctness & completeness
   - Robustness & edge cases
   - Clarity, intent & elegance
   - Simplicity & duplication
   - Polish & production readiness

   Prioritize by impact.

3. **Improve**  
   Execute the highest-leverage fixes first. Make precise, high-signal changes. Apply the domain guidance below.

4. **Review**  
   Hold the new version against the Quality Bar. Would an experienced practitioner call it excellent, or still say "needs more work"?

5. **Decide**  
   Continue only if a clear, high-impact improvement remains that does not add disproportionate complexity or speculation.  
   Otherwise stop.

6. **Deliver**

Do not iterate for its own sake. Stop when additional changes deliver only marginal or cosmetic value.

## Quality Bar

The result must feel *noticeably* better than a competent answer.

**Correct** — accurate, no bugs, truthful in its domain.  
**Robust** — handles edges, errors, and ambiguity without drama.  
**Clean** — simple, no unnecessary parts.  
**Elegant** — right structure and abstractions; feels deliberate.  
**Useful** — immediately practical and high-value.  
**Direct** — zero fluff, zero hedging, zero filler.  
**Practical** — ready or nearly ready for real use.  
**Polished** — consistent, professional, no rough edges.  
**Maintainable** — obvious for the next person (or agent).  
**Strong** — functionally or emotionally superior to the obvious version.

If it merely "works" or is "pretty good", continue.

## For Code Tasks

- Establish correctness first.
- Fix bugs before style.
- Simplify ruthlessly (delete what is not earning its keep).
- Improve names, boundaries, and structure.
- Eliminate duplication and accidental complexity.
- Make error paths and edges explicit.
- Add or improve tests only where they add real safety.
- Respect existing conventions unless they are the problem.
- Introduce dependencies only when the net benefit is obvious.
- Prefer precise edits over heroic rewrites.

Final code should not merely run — it should look like the obviously correct solution.

## For Writing Tasks

- Keep the user's real voice and tone.
- Cut awkwardness, filler, and vagueness.
- Make it clearer, tighter, more natural.
- Strengthen impact or persuasion where it serves the goal.
- Kill corporate speak, jargon, and weak hedging.
- Keep it human and direct.
- Make it scannable and immediately usable.

Final text should read like the user — only sharper and stronger.

## For Design, Product, or Strategy Tasks

- Make the core idea unmistakable.
- Clarify value and real differentiation.
- Remove generic language and buzz.
- Make it distinctive and ownable.
- Expose weak assumptions and missing constraints.
- Improve usability and practicality.
- Give it premium, intentional feel.

The result should be easier to grasp, easier to champion, and harder to ignore.

## For Prompts and Agent Instructions

- Sharpen role and objective.
- Eliminate ambiguity and loose ends.
- Define success criteria and quality bar explicitly.
- Add high-leverage constraints.
- Reduce derailment and hallucination surface.
- Improve output structure and format.
- Keep original intent intact.

The final prompt should get better results with less hand-holding.

## Critical Rules

- Do not alter the user's core goal.
- Do not invent features or expand scope.
- Do not add complexity for cleverness.
- Do not do vague "make it better" — be surgical.
- Do not hide uncertainty or overclaim.
- Do not stop at surface-level polish.
- Do not ask for clarification when a strong improvement is already possible.
- Do not iterate past the point of real value.

## Output Format

Return **exactly** this structure. No intro, no closing zinger, no extra sections.

1. **The improved result**  
   The full upgraded artifact (complete code, text, plan, prompt, design, etc.). Format appropriately.

2. **What was made better**  
   Concise, specific bullets naming the highest-leverage upgrades only. Concrete. No generic compliments.

3. **Remaining risks or open points**  
   Only if material. Be honest and brief. Omit the section entirely when nothing worth mentioning remains.

Be direct. Be critical. Be useful.  
**Mach geil.**

## How to Invoke

Users trigger with natural language:

- "mach geil this"
- "make this geil"
- "improve until you can't make it better"
- "don't stop at good enough"
- "polish / elevate / productionize this"
- "mach geil the [component / prompt / plan / copy / design]"

When any of these patterns appear on an existing artifact, run the full loop.

## Stopping Condition

Stop when:
- Major weaknesses from the audit are resolved.
- The result clearly meets the Quality Bar.
- Further changes would be marginal, speculative, or add complexity for little gain.

A good stopping signal: an experienced reviewer would call the result excellent rather than "needs more work".

Deliver. No apology. No "let me know if you want another round".

# OpenAI Debate Adaptation

This skill adapts OpenAI's AI safety via debate framing to algorithm and architecture review.

Primary sources:

- OpenAI, "AI Safety via Debate": https://openai.com/index/debate/
- Irving, Christiano, and Amodei, "AI Safety via Debate": https://arxiv.org/abs/1805.00899

## What To Preserve

OpenAI's debate setup uses opposing agents to make a hard judgment easier for a judge. Each side exposes facts, assumptions, and weaknesses that the judge may not have found alone.

Preserve these properties:

- Competing sides must start from the same evidence.
- Each side must argue truthfully from the evidence, not optimize for rhetorical victory.
- The judge's job must be easier than solving the original problem from scratch.
- Debate should decompose the decision into smaller cruxes the judge can inspect.
- The final answer should identify which claims mattered and why.

## Algorithm Review Translation

Map debate roles this way:

| Debate role | Algorithm review role |
| --- | --- |
| Proponent of answer A | Defender of the current path |
| Proponent of answer B | Challenger with a concrete alternative |
| Human judge | Codex judge using a rubric and evidence |
| Debate transcript | Crux-focused comparison, not a transcript dump |
| Winning argument | Decision: keep, modify, replace, or investigate |

## Practical Constraints

Use bounded debate. One independent pass plus one cross-examination round is usually enough. Add a second round only when it surfaces new evidence or a sharper test.

Use verification whenever possible. If a claim can be tested with a fixture, benchmark, trace query, type check, log slice, or counterexample, prefer that over another debate round.

Keep the judge bias-aware. The judge should ignore verbosity, rotate argument order mentally, and decide against the stated success criteria.

Make the Challenger constructive. The Challenger must either propose a concrete alternative or specify the cheapest discriminating check that would reveal whether the current path fails.

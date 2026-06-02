---
name: judge-decision
description: Run an OpenAI-debate-inspired judged decision workflow for an algorithm, ranking strategy, architecture path, heuristic, optimization, data pipeline, implementation approach, product decision, or technical plan. Use when the user asks to challenge the path taken, argue against an approach, compare competing approaches, appoint a judge, use Claude as an external judge, run a judge-of-judge workflow, run a devil's-advocate review, find a better route, or make a decision where one plausible path may hide correctness, complexity, scaling, maintainability, product, or operational risks.
---

# Judge Decision

## Overview

Use this skill to stress-test a technical or product path before committing to it. The operating model is based on OpenAI's debate framing: make the hard decision easier for a judge by forcing opposing agents to expose the strongest evidence, assumptions, counterexamples, and concrete alternatives.

For the source adaptation, read `references/openai-debate-adaptation.md` when the user asks why this protocol works, wants to modify the protocol, or needs a more formal debate setup.

## Core Rule

Do not produce a loose transcript. Produce a decision-ready review: the current path, the best objection, the best alternative, the evidence that matters, the external judge's view when requested, the judge-of-judge verdict, and the cheapest next check if uncertainty remains.

## Workflow

1. Frame the decision.
   - Name the current algorithm or path being challenged.
   - State the decision to make: keep, modify, replace, or investigate.
   - Define success criteria and constraints: correctness, latency, throughput, cost, maintainability, explainability, product behavior, migration risk, or other user priorities.
   - Identify the real unit of analysis before gathering broad evidence.

2. Gather the evidence bundle.
   - Inspect relevant code, tests, logs, specs, traces, data shapes, benchmarks, or production behavior before debating.
   - Keep evidence separate from inference.
   - Prefer direct measurements and executable checks over model opinion when a claim is testable.

3. Run independent first passes.
   - The Defender argues the strongest truthful case for the path taken.
   - The Challenger argues the strongest truthful case against the path and must propose at least one concrete alternative.
   - Keep the first passes independent. If subagents are available, give each the same compact evidence bundle and role prompt. If not, write the passes sequentially without letting the second pass reuse the first as a crutch.

4. Cross-examine the cruxes.
   - Give each side one round to identify the opponent's weakest assumption, missing edge case, or unsupported claim.
   - Limit to one or two rounds. Stop when new claims are repetitive or when a deterministic check would answer the dispute better.

5. Verify what can be verified.
   - Run or propose focused tests, counterexamples, complexity analysis, benchmark slices, schema checks, simulations, or trace inspections.
   - If verification cannot be done in the current turn, specify the exact command, fixture, metric, or experiment that would discriminate between the paths.

6. Optionally call Claude as first judge.
   - Use this step when the user asks for Claude, an external judge, a second model, or a judge-of-judge workflow.
   - Use the `call-claude` skill if it is available.
   - Send Claude a compact evidence bundle with the current path, Defender case, Challenger case, verification evidence, constraints, and requested output contract.
   - Ask Claude to judge the algorithmic decision, not to continue the debate.
   - Treat Claude's response as evidence for the final judge, not as the final answer.

7. Run the judge-of-judge.
   - Compare the Defender, Challenger, verification evidence, and Claude's judgment.
   - Identify where Claude added a genuinely new angle, where it repeated known points, and where it made unsupported assumptions.
   - Verify any testable claim from Claude before adopting it when feasible.
   - Decide whether Claude's judgment changes the outcome, changes only the next check, or should be rejected.

8. Judge with a rubric.
   - Score the arguments against the user's stated priorities, not against eloquence or confidence.
   - Rotate or relabel argument order mentally to reduce position bias.
   - Penalize vague critique, untestable claims, and alternatives that do not preserve required behavior.
   - Reward evidence, concrete failure modes, simpler invariants, clear complexity wins, and cheap verification paths.

9. Report the result.
   - Lead with the verdict.
   - Include the decisive cruxes and evidence.
   - Include the Claude judge result and judge-of-judge assessment when Claude was used.
   - State the recommended next action.
   - Include residual uncertainty only where it changes the next step.

## Role Prompts

Use these prompts directly when spawning subagents, or as internal passes when no subagent tool is available.

### Defender

You are the Path Defender. Make the strongest truthful case for the current algorithm or design path. Cite evidence from the bundle. Explain why the path is correct enough, operationally acceptable, and preferable under the stated constraints. Name the weakest part of your case.

### Challenger

You are the Path Challenger. Make the strongest truthful case against the current algorithm or design path. Find edge cases, false assumptions, complexity risks, scaling risks, maintainability problems, and product behavior mismatches. Propose at least one concrete alternative and explain what evidence would prove it better.

### Judge

You are the Judge. Decide between the current path, a modified version, the alternative path, or further investigation. Use the rubric and evidence. Do not reward verbosity. Identify the decisive crux, unresolved risks, and the cheapest discriminating check if the verdict is not fully settled.

### Claude External Judge

You are Claude acting as the first external judge. Review the current path, Defender case, Challenger case, and verification evidence. Bring a different angle where useful, but stay grounded in the supplied evidence. Decide keep, modify, replace, or investigate. Return the strongest missed risk, best concrete alternative, confidence, and the evidence that most affected your view.

### Judge-of-Judge

You are the final judge-of-judge. Evaluate Claude's judgment alongside the Defender, Challenger, and verification evidence. Decide what Claude got right, what Claude missed or assumed, whether Claude changed the decision, and what the final action should be.

## Default Rubric

Adjust weights when the user gives priorities.

| Dimension | Default weight | What to inspect |
| --- | ---: | --- |
| Correctness and invariants | 30 | Required behavior, edge cases, data assumptions, failure modes |
| Complexity and maintainability | 20 | Simplicity, local patterns, cognitive load, extensibility |
| Performance and scaling | 20 | Runtime complexity, latency, throughput, memory, queue behavior |
| Operational and migration risk | 15 | Rollout, observability, retries, backfills, compatibility |
| Testability and evidence | 15 | Existing tests, cheap checks, benchmarkability, reproducibility |

## Output Format

```markdown
**Verdict**
Keep / Modify / Replace / Investigate: <one-sentence decision>

**Current Path**
<brief description>

**Best Defense**
<strongest evidence-backed case for the current path>

**Best Challenge**
<strongest evidence-backed objection and concrete alternative>

**Claude Judge**
<Claude's verdict and strongest useful point; write "not used" if Claude was unavailable or not requested>

**Judge-of-Judge**
<assessment of Claude's feedback against the evidence, including what changed or did not change>

**Judge's Reasoning**
<rubric-based comparison, decisive cruxes, and why the losing path loses>

**Next Check**
<specific test, benchmark, log query, code inspection, or experiment; omit only when the verdict is already fully supported>
```

## Failure Modes

- Do not let the Challenger merely complain. Require a concrete alternative or a concrete discriminating test.
- Do not let the Defender claim "existing behavior" is enough without evidence that the behavior is intentional and complete.
- Do not let the Judge decide from confidence, polish, length, or first-position advantage.
- Do not let Claude's external judgment override direct evidence or tool-verifiable checks.
- Do not discard Claude's feedback just because it disagrees with Codex; first identify whether it found a real crux.
- Do not continue debate rounds after the disagreement has become testable.
- Do not bury the decision below the debate details.

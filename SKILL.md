---
name: judge-decision
description: Run an OpenAI-debate-inspired judged decision workflow for technical, product, architecture, algorithm, ranking, data, debugging, implementation, or planning choices. Use when the user asks to help decide, challenge a path, argue against an approach, compare competing approaches, sanity-check a plan, appoint a judge, call Claude or Codex as an external judge, run a judge-of-judge workflow, run a devil's-advocate review, find a better route, identify what they are missing, or make a decision where one plausible path may hide correctness, complexity, scaling, maintainability, product, user, operational, or evidence risks.
---

# Judge Decision

## Overview

Use this skill to turn a hard choice into a decision record. The operating model is based on OpenAI's debate framing: make the hard decision easier for a judge by forcing opposing agents to expose the strongest evidence, assumptions, counterexamples, and concrete alternatives.

For the source adaptation, read `references/openai-debate-adaptation.md` when the user asks why this protocol works, wants to modify the protocol, or needs a more formal debate setup.

## Core Rule

Do not produce a loose transcript. Produce a decision-ready review: the current path, the best objection, the best alternative, the evidence that matters, any real external judge's view when available, what that judge changed, the judge-of-judge verdict, decision status, and the cheapest next check if uncertainty remains. The skill must work without any companion skills installed.

## Example Triggers

- "Use $judge-decision to decide whether this ranking algorithm should stay."
- "Challenge this architecture path and bring in an external judge if one is available."
- "I think this debugging hypothesis is right. What am I missing?"
- "Compare these two implementation approaches and make the call."
- "Run a judge-of-judge on Claude's feedback before we decide."

## Decision Modes

- **Standard**: Use when no external judge is requested or available. Run Defender, Challenger, verification, and final judge.
- **External-judge assisted**: Use when the user asks for Claude, Codex, a second model, or a judge-of-judge. Route to a real cross-model judge when available, then assess that feedback in the final judge-of-judge.
- **Fast path**: Use when the user needs a quick call. Still require a concrete objection, one alternative, the decisive evidence, and the cheapest next check.

## Workflow

1. Frame the decision.
   - Name the current algorithm or path being challenged.
   - State the decision to make: keep, modify, replace, or investigate.
   - Define success criteria and constraints: correctness, latency, throughput, cost, maintainability, explainability, product behavior, migration risk, or other user priorities.
   - Identify the real unit of analysis before gathering broad evidence.
   - Set a decision status target: Settled, Provisional, or Blocked.

2. Gather the evidence bundle.
   - Inspect relevant code, tests, logs, specs, traces, data shapes, benchmarks, or production behavior before debating.
   - Keep evidence separate from inference.
   - Prefer direct measurements and executable checks over model opinion when a claim is testable.
   - If evidence is thin, say what is missing and whether the decision can still be provisional.
   - Keep the bundle compact enough that each judge sees the same cruxes, not an undifferentiated dump.

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

6. Select the external judge when requested.
   - Use this step when the user asks for Claude, Codex, an external judge, a second model, or a judge-of-judge workflow.
   - First identify the caller context from the runtime, system context, user request, or surrounding toolchain. The caller is the model currently executing this skill, not the subject being discussed. Record it as `caller_model` when it is known.
   - Check for real external-judge mechanisms before committing to a route. Examples include host-provided tools, local authenticated CLIs, user-provided external output, or optional companion skills such as `call-claude` and `call-codex`. Do not assume any of these are installed.
   - If the caller is Codex, OpenAI, or GPT-family, prefer a Claude/Anthropic-family external judge when a real mechanism is available.
   - If the caller is Claude or Anthropic-family, prefer a Codex/OpenAI-family external judge when a real mechanism is available.
   - If the caller is unknown, choose by decision type: prefer a Claude/Anthropic-family judge for taste, design, writing, product judgment, UX, brand, narrative, naming, and human-preference questions; prefer a Codex/OpenAI-family judge for deep logic, algorithms, correctness, invariants, architecture, debugging, tests, edge cases, data flow, concurrency, security, performance, and proof-like technical decisions.
   - If the preferred external judge family is unavailable but another cross-model judge is available and not the same model family as the caller, use the available cross-model judge and state the fallback.
   - If the user explicitly names the external judge, honor that request unless the tool is unavailable.
   - If no suitable external judge mechanism is available, state that it is unavailable and continue with the internal judge-of-judge rather than pretending to have cross-model signal.

7. Call the selected external judge.
   - Use the environment's best available adapter. Optional companion skills are convenient adapters, not dependencies of this skill.
   - Send a compact evidence bundle with the current path, Defender case, Challenger case, verification evidence, constraints, caller context, routing reason, and requested output contract.
   - Ask the external judge to judge the decision, not to continue the debate.
   - Treat the external judge's response as evidence for the final judge, not as the final answer.

8. Run the judge-of-judge.
   - Compare the Defender, Challenger, verification evidence, and external judge's judgment.
   - Identify where the external judge added a genuinely new angle, where it repeated known points, and where it made unsupported assumptions.
   - Verify any testable claim from the external judge before adopting it when feasible.
   - Decide whether the external judgment changes the outcome, changes only the next check, or should be rejected.
   - Classify the effect as: changed verdict, changed next check, added useful risk, repeated known points, unsupported, or not used.

9. Judge with a rubric.
   - Score the arguments against the user's stated priorities, not against eloquence or confidence.
   - Rotate or relabel argument order mentally to reduce position bias.
   - Penalize vague critique, untestable claims, and alternatives that do not preserve required behavior.
   - Reward evidence, concrete failure modes, simpler invariants, clear complexity wins, and cheap verification paths.

10. Report the result.
   - Lead with the verdict.
   - Include the decisive cruxes and evidence.
   - Include the external judge result, selected model, routing reason, and judge-of-judge assessment when an external judge was used.
   - State the recommended next action.
   - Include residual uncertainty only where it changes the next step.
   - End with decision status: Settled, Provisional, or Blocked.

## Role Prompts

Use these prompts directly when spawning subagents, or as internal passes when no subagent tool is available.

### Defender

You are the Path Defender. Make the strongest truthful case for the current algorithm or design path. Cite evidence from the bundle. Explain why the path is correct enough, operationally acceptable, and preferable under the stated constraints. Name the weakest part of your case.

### Challenger

You are the Path Challenger. Make the strongest truthful case against the current algorithm or design path. Find edge cases, false assumptions, complexity risks, scaling risks, maintainability problems, and product behavior mismatches. Propose at least one concrete alternative and explain what evidence would prove it better.

### Judge

You are the Judge. Decide between the current path, a modified version, the alternative path, or further investigation. Use the rubric and evidence. Do not reward verbosity. Identify the decisive crux, unresolved risks, and the cheapest discriminating check if the verdict is not fully settled.

### External Judge

You are the first external judge. Review the current path, Defender case, Challenger case, caller context, routing reason, and verification evidence. Bring a different angle where useful, but stay grounded in the supplied evidence. Decide keep, modify, replace, or investigate. Return the strongest missed risk, best concrete alternative, confidence, and the evidence that most affected your view.

Use this generic prompt when the selected external judge is not specifically Claude or Codex.

### Claude External Judge

You are Claude acting as the first external judge. Review the current path, Defender case, Challenger case, caller context, routing reason, and verification evidence. Bring taste, product, writing, UX, strategic, and human-preference nuance where relevant, but stay grounded in the supplied evidence. Decide keep, modify, replace, or investigate. Return the strongest missed risk, best concrete alternative, confidence, and the evidence that most affected your view.

### Codex External Judge

You are Codex acting as the first external judge. Review the current path, Defender case, Challenger case, caller context, routing reason, and verification evidence. Bring correctness, invariant, edge-case, implementation, architecture, and test-strategy rigor where relevant, but stay grounded in the supplied evidence. Decide keep, modify, replace, or investigate. Return the strongest missed risk, best concrete alternative, confidence, and the evidence that most affected your view.

### Judge-of-Judge

You are the final judge-of-judge. Evaluate the selected external judge's judgment alongside the Defender, Challenger, and verification evidence. Decide what the external judge got right, what it missed or assumed, whether it changed the decision, and what the final action should be.

## Evidence Bundle Template

Use this structure before debating when the input is messy:

```markdown
**Decision**
<what must be decided>

**Current Path**
<current approach>

**Alternatives Known**
<candidate alternatives or "unknown">

**Constraints**
<business, product, technical, operational, timeline>

**Evidence**
<facts with anchors>

**Open Questions**
<unknowns that affect the decision>

**Cheap Checks**
<tests, queries, benchmarks, traces, or inspections>
```

## Decision Status

- **Settled**: the evidence is strong enough to act without another discriminating check.
- **Provisional**: the current best call is clear, but one cheap check, missing measurement, or implementation detail could change the decision.
- **Blocked**: required evidence, access, context, or a requested external judge is unavailable and guessing would be risky.

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

**External Judge**
<caller context, available external-judge mechanisms, selected model/tool if any, routing reason, verdict, and strongest useful point; write "not used" if unavailable or not requested>

**What External Judge Changed**
Changed verdict / Changed next check / Added useful risk / Repeated known points / Unsupported / Not used: <one-sentence explanation>

**Judge-of-Judge**
<assessment of the external judge's feedback against the evidence, including what changed or did not change>

**Judge's Reasoning**
<rubric-based comparison, decisive cruxes, and why the losing path loses>

**Next Check**
<specific test, benchmark, log query, code inspection, or experiment; omit only when the verdict is already fully supported>

**Decision Status**
Settled / Provisional / Blocked: <why>
```

## Failure Modes

- Do not let the Challenger merely complain. Require a concrete alternative or a concrete discriminating test.
- Do not let the Defender claim "existing behavior" is enough without evidence that the behavior is intentional and complete.
- Do not let the Judge decide from confidence, polish, length, or first-position advantage.
- Do not route to the same model family as the caller when a cross-model external judge is available.
- Do not make companion skills a hard dependency. This published skill must remain self-contained.
- Do not pretend an external adapter exists. Check availability before invoking or promising a tool, CLI, host capability, or optional companion skill.
- Do not hide the routing basis. If caller context is unknown, say which domain heuristic selected the judge.
- Do not let an external judgment override direct evidence or tool-verifiable checks.
- Do not discard external feedback just because it disagrees with the caller; first identify whether it found a real crux.
- Do not treat a provisional decision as settled just because the arguments sound confident.
- Do not continue debate rounds after the disagreement has become testable.
- Do not bury the decision below the debate details.

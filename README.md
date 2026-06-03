# Judge Decision

This skill is for the moment where "this seems right" is not enough.

It turns a technical or product decision into a structured judgment. The point is not to create a dramatic debate transcript. The point is to force the current path through the right pressure:

- What exactly are we deciding?
- What is the strongest case for the current path?
- What is the strongest case against it?
- What evidence can we actually verify?
- If another model is available, does it add a real new angle or just repeat the obvious?
- What should we do next?

Use it when a plausible answer may be hiding correctness, complexity, scaling, maintainability, product, or operational risk.

## What It Does

The skill runs a decision workflow:

1. Frame the decision, constraints, and success criteria.
2. Gather the evidence bundle: code, tests, logs, traces, specs, metrics, benchmarks, or production behavior.
3. Run a Defender pass for the current path.
4. Run a Challenger pass against the current path, with a concrete alternative.
5. Verify whatever can be verified instead of arguing from vibes.
6. Use a real external judge when one is available.
7. Produce a final judge-of-judge verdict with the next check if uncertainty remains.

This is useful for architecture decisions, ranking or recommendation algorithms, implementation approaches, data pipelines, scaling tradeoffs, product judgment, design direction, and any path where the wrong choice would be expensive later.

## External Judges

The skill is self-contained. It does not require `call-claude`, `call-codex`, or any other companion skill.

When an external judge is requested, it first checks whether a real external mechanism exists. That can come from the host environment, a local authenticated CLI, user-provided external-model output, or an optional adapter skill.

Routing works like this:

- Codex/OpenAI callers prefer a Claude/Anthropic-family external judge when available.
- Claude/Anthropic callers prefer a Codex/OpenAI-family external judge when available.
- Unknown callers route by the decision type: Claude/Anthropic-family judgment for taste, UX, writing, product, naming, and human-preference questions; Codex/OpenAI-family judgment for correctness, algorithms, invariants, architecture, debugging, tests, security, performance, and implementation rigor.

If no real external judge is available, the skill says that clearly and continues internally. It does not simulate cross-model signal.

Optional adapters can still make the workflow better:

- `call-claude`
- `call-codex`

But they are adapters, not dependencies.

## When To Use It

Use this when you want the decision to survive scrutiny:

- "Challenge this architecture before we commit."
- "Is this ranking algorithm actually the right path?"
- "Compare these two implementation approaches."
- "Run a judge-of-judge workflow on this plan."
- "Find the strongest objection and the cheapest way to test it."

Do not use it for a casual second opinion where no evidence is available and no decision needs to be made. The value comes from forcing the claim against evidence.

## Install

Clone the public repo, then install it under the canonical skill name:

```bash
git clone https://github.com/uncoooloj/judge-decision-skill.git
mkdir -p ~/.codex/skills/judge-decision
cp -R judge-decision-skill/SKILL.md judge-decision-skill/agents judge-decision-skill/references ~/.codex/skills/judge-decision/
```

The repo uses the public `-skill` suffix. The installed skill name stays clean: `judge-decision`.

Invoke it with:

```text
Use $judge-decision to challenge this approach, use an external judge if available, and produce a final decision.
```

## Output Shape

The final answer should lead with the verdict, not bury it under debate notes:

- verdict: keep, modify, replace, or investigate
- current path
- best defense
- best challenge
- external judge result, if a real one was available
- judge-of-judge assessment
- reasoning against the rubric
- next check

## Files

- `SKILL.md` - the skill instructions and output contract
- `agents/openai.yaml` - Codex UI metadata
- `references/openai-debate-adaptation.md` - source framing and adaptation notes
- `references/issue-pr-register.md` - improvement issue and grouped PR map

## License

No license has been selected yet.

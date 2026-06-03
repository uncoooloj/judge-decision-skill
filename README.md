# Judge Decision

A Codex skill for turning hard technical or product choices into evidence-backed decisions.

The workflow adapts debate-style review into a practical engineering loop:

- frame the decision and constraints
- gather the evidence bundle
- run a Defender and Challenger pass
- verify testable claims
- choose a caller-aware external judge, using Claude or Codex when available
- produce a final judge-of-judge verdict

Use it for architecture decisions, ranking or recommendation algorithms, implementation approaches, data pipelines, scaling tradeoffs, product judgment, design direction, or any path where one plausible answer may hide correctness, complexity, performance, product, or operational risks.

When an external judge is requested, the skill now routes by caller context first:

- Codex/OpenAI callers prefer Claude via `call-claude`
- Claude/Anthropic callers prefer Codex via `call-codex`
- unknown callers fall back to the decision type: Claude for taste/design/product judgment, Codex for deep logic/correctness/implementation rigor

## Install

Copy this folder into your Codex skills directory:

```bash
cp -R judge-decision ~/.codex/skills/judge-decision
```

Then invoke it in Codex with:

```text
Use $judge-decision to challenge this approach, choose the right external judge, and produce a final decision.
```

## Files

- `SKILL.md` - the skill instructions and output contract
- `agents/openai.yaml` - Codex UI metadata
- `references/openai-debate-adaptation.md` - source framing and adaptation notes

## License

No license has been selected yet.

# Judge Decision

A Codex skill for turning hard technical or product choices into evidence-backed decisions.

The workflow adapts debate-style review into a practical engineering loop:

- frame the decision and constraints
- gather the evidence bundle
- run a Defender and Challenger pass
- verify testable claims
- choose a caller-aware external judge when a real external mechanism is available
- produce a final judge-of-judge verdict

Use it for architecture decisions, ranking or recommendation algorithms, implementation approaches, data pipelines, scaling tradeoffs, product judgment, design direction, or any path where one plausible answer may hide correctness, complexity, performance, product, or operational risks.

This published skill has no hard dependency on other skills. When an external judge is requested, it routes by caller context first:

- Codex/OpenAI callers prefer a Claude/Anthropic-family external judge when available
- Claude/Anthropic callers prefer a Codex/OpenAI-family external judge when available
- unknown callers fall back to the decision type: Claude/Anthropic-family judgment for taste/design/product judgment, Codex/OpenAI-family judgment for deep logic/correctness/implementation rigor

External judge mechanisms can come from the host environment, a local authenticated CLI, user-provided external-model output, or optional adapter skills such as:

- `call-claude`
- `call-codex`

If no real external mechanism is available, `judge-decision` should say that and continue with the internal judge-of-judge. It should not simulate an external judge or hide the missing capability.

## Install

Copy this folder into your Codex skills directory:

```bash
cp -R judge-decision ~/.codex/skills/judge-decision
```

Then invoke it in Codex with:

```text
Use $judge-decision to challenge this approach, use an external judge if available, and produce a final decision.
```

## Files

- `SKILL.md` - the skill instructions and output contract
- `agents/openai.yaml` - Codex UI metadata
- `references/openai-debate-adaptation.md` - source framing and adaptation notes

## License

No license has been selected yet.

# Issue and PR Register

This register tracks the first public improvement wave for `judge-decision-skill`.

## Judge Decision Issues

| Issue | Title | Grouped PR |
| --- | --- | --- |
| [#6](https://github.com/uncoooloj/judge-decision-skill/issues/6) | Broaden plain-language trigger coverage | [#18 Improve Judge Decision discovery and modes](https://github.com/uncoooloj/judge-decision-skill/pull/18) |
| [#4](https://github.com/uncoooloj/judge-decision-skill/issues/4) | Add memorable invocation examples | [#18 Improve Judge Decision discovery and modes](https://github.com/uncoooloj/judge-decision-skill/pull/18) |
| [#5](https://github.com/uncoooloj/judge-decision-skill/issues/5) | Add explicit decision modes | [#18 Improve Judge Decision discovery and modes](https://github.com/uncoooloj/judge-decision-skill/pull/18) |
| [#3](https://github.com/uncoooloj/judge-decision-skill/issues/3) | Make decision status first-class | [#19 Strengthen evidence status and output contract](https://github.com/uncoooloj/judge-decision-skill/pull/19) |
| [#10](https://github.com/uncoooloj/judge-decision-skill/issues/10) | Add reusable evidence bundle template | [#19 Strengthen evidence status and output contract](https://github.com/uncoooloj/judge-decision-skill/pull/19) |
| [#9](https://github.com/uncoooloj/judge-decision-skill/issues/9) | Classify external judge contribution | [#19 Strengthen evidence status and output contract](https://github.com/uncoooloj/judge-decision-skill/pull/19) |
| [#11](https://github.com/uncoooloj/judge-decision-skill/issues/11) | Add What External Judge Changed output section | [#19 Strengthen evidence status and output contract](https://github.com/uncoooloj/judge-decision-skill/pull/19) |
| [#12](https://github.com/uncoooloj/judge-decision-skill/issues/12) | Clarify settled provisional and blocked decisions | [#19 Strengthen evidence status and output contract](https://github.com/uncoooloj/judge-decision-skill/pull/19) |
| [#7](https://github.com/uncoooloj/judge-decision-skill/issues/7) | Forbid simulated external judges | [#20 Make external judge handoff safer and deterministic](https://github.com/uncoooloj/judge-decision-skill/pull/20) |
| [#8](https://github.com/uncoooloj/judge-decision-skill/issues/8) | Add exact external judge prompt template | [#20 Make external judge handoff safer and deterministic](https://github.com/uncoooloj/judge-decision-skill/pull/20) |
| [#15](https://github.com/uncoooloj/judge-decision-skill/issues/15) | Reduce call-claude workflow overlap | [call-claude-skill #2 Narrow and harden the Claude CLI helper](https://github.com/uncoooloj/call-claude-skill/pull/2) |
| [#17](https://github.com/uncoooloj/judge-decision-skill/issues/17) | Harden call-claude availability guidance | [call-claude-skill #2 Narrow and harden the Claude CLI helper](https://github.com/uncoooloj/call-claude-skill/pull/2) |
| [#16](https://github.com/uncoooloj/judge-decision-skill/issues/16) | Clarify call-claude as helper not workflow | [call-claude-skill #2 Narrow and harden the Claude CLI helper](https://github.com/uncoooloj/call-claude-skill/pull/2) |
| [#13](https://github.com/uncoooloj/judge-decision-skill/issues/13) | Support large Claude evidence bundles | [call-claude-skill #2 Narrow and harden the Claude CLI helper](https://github.com/uncoooloj/call-claude-skill/pull/2) |
| [#14](https://github.com/uncoooloj/judge-decision-skill/issues/14) | Add issue and PR traceability register | This register PR |

## Grouping Rationale

- Discovery and modes stay together because they shape when the skill is invoked.
- Evidence, status, and output contract stay together because they define the final decision record.
- External-judge handoff stays separate because it is about adapter honesty and prompt determinism.
- `call-claude` hardening is implemented in the companion repo but closes the adapter issues tracked here.
- Traceability is kept separate so reviewers can audit the issue-to-PR mapping without mixing it into behavior changes.

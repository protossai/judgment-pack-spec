# Judgment Pack Specification

> **Status: Research Preview — `0.1.0-draft`**
>
> This repository is an early design proposal. It is not an industry standard, is not covered by
> compatibility guarantees, and is not ready for consequential production decisions.

The Judgment Pack Specification (JPS) explores a portable, vendor-neutral way to represent
reusable organizational judgment.

Traditional knowledge systems answer “what information is available?” A Judgment Pack is intended
to make a different artifact portable: what decision is being made, what evidence it requires,
where its claims came from, when it applies, how exceptions and uncertainty are handled, which
outcomes are possible, and when a human must take over.

## Why this repository exists

AI systems increasingly participate in business decisions, but prompts, retrieval configurations,
and application code do not provide a stable interchange format for organizational judgment. This
research preview exists to test whether a small declarative core can support independent tools and
runtimes without standardizing any one product, model, or workflow.

The specification is successful only if independent implementations can exchange the same pack and
agree on its structure and portable semantics.

## What JPS is

- A JSON-based interchange format for decision intent, evidence requirements, rules, exceptions,
  outcomes, uncertainty handling, escalation, sources, and traceability.
- A specification that separates document conformance from factual truth and organizational
  authorization.
- A potential foundation for validators, authoring tools, registries, evaluation systems, and
  execution engines maintained by different vendors.

## What JPS is not

- A claim that a conforming pack is correct, safe, authorized, or suitable for a particular use.
- A prompt format, chain-of-thought format, retrieval API, workflow engine, or general-purpose rule
  language.
- A standard for model selection, inference, optimization, calibration, enterprise identity, or
  user-interface behavior.
- A stable standard. All `0.x` material may change incompatibly.

## Repository contents

| Path                                                                               | Purpose                                     |
| ---------------------------------------------------------------------------------- | ------------------------------------------- |
| [`spec/judgment-pack-core.md`](spec/judgment-pack-core.md)                         | Draft normative core                        |
| [`schema/judgment-pack-core.schema.json`](schema/judgment-pack-core.schema.json)   | Draft 2020-12 structural schema             |
| [`examples/minimal-expense-approval.json`](examples/minimal-expense-approval.json) | Minimal end-to-end example                  |
| [`conformance/`](conformance/)                                                     | Seed positive and negative structural cases |
| [`docs/design-principles.md`](docs/design-principles.md)                           | Design constraints                          |
| [`docs/non-goals.md`](docs/non-goals.md)                                           | Explicit boundary                           |
| [`docs/origin-and-boundary.md`](docs/origin-and-boundary.md)                       | Relationship to Protoss                     |
| [`jeps/0000-jep-process.md`](jeps/0000-jep-process.md)                             | Proposed change process                     |
| [`ROADMAP.md`](ROADMAP.md)                                                         | Evidence-gated path toward a specification  |

## Minimal shape

```json
{
  "specVersion": "0.1.0-draft",
  "id": "https://example.com/judgment-packs/expense-approval",
  "version": "0.1.0",
  "title": "Expense approval",
  "decision": {
    "intent": "Determine whether an expense may be automatically approved.",
    "question": "May this expense be approved without human review?"
  },
  "outcomes": [
    { "id": "approve", "label": "Approve" },
    { "id": "manual-review", "label": "Manual review" }
  ],
  "rules": [
    {
      "id": "large-expense",
      "description": "Large expenses require a human decision.",
      "when": {
        "op": "fact",
        "path": "/expense/amount",
        "operator": "greater-than",
        "value": "5000"
      },
      "outcome": "manual-review",
      "onUnknown": "escalate"
    }
  ],
  "fallbackOutcome": "approve"
}
```

See the complete example before relying on this abbreviated shape.

## Conformance, truth, and authority

These are deliberately separate claims:

1. **Structural conformance:** the document satisfies the JPS schema.
2. **Semantic conformance:** references and portable rules satisfy the prose specification.
3. **Factual grounding:** evidence supports the pack's claims.
4. **Organizational authorization:** an accountable organization permits the pack's use.
5. **Operational fitness:** a particular runtime applies it safely and effectively.

This research preview specifies only the beginnings of the first two. A validator must never imply
the remaining three.

## Participate

Start with [`CONTRIBUTING.md`](CONTRIBUTING.md). Material changes should begin as a Judgment
Enhancement Proposal (JEP), include compatibility and security analysis, and add positive and
negative examples.

The project is initially stewarded by Protoss AI. Stewardship does not make Protoss implementations
normative; the long-term goal is reproducible behavior across independent implementations.

## License

Licensed under the Apache License 2.0. See [`LICENSE`](LICENSE).

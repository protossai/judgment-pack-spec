# Why Judgment Pack?

## The gap between information and judgment

AI agents can retrieve information, call tools, and complete multi-step tasks. But access to information
is not the same as the ability to make a sound decision. Documents and knowledge bases can tell an agent
what an organization knows; they rarely define, in an executable and testable form: which evidence
matters; when a rule applies; which exceptions change the outcome; how conflicting evidence is handled;
how uncertainty affects a conclusion; when the agent must abstain; when a human must be involved; and how
the decision should be evaluated.

Today this judgment is scattered across policies, procedures, prompts, application code, examples, expert
experience, and evaluation datasets. As a result it is hard to inspect, test, version, reuse, audit, or
move between agent systems. Judgment Pack provides a portable, vendor-neutral representation for that
missing judgment layer.

## What a Judgment Pack connects

A Judgment Pack brings the parts of a decision into one inspectable structure:

- the decision being made;
- the evidence that may support it;
- applicability conditions that determine when the pack applies;
- decision rules;
- exceptions that override the ordinary outcome;
- uncertainty boundaries;
- abstention and escalation criteria;
- expected outputs;
- provenance for sources and claims;
- evaluation cases that describe expected behavior.

The purpose is not to replace human judgment or prescribe one universal reasoning method. It is to make
important judgment explicit enough that people and machines can inspect, test, challenge, improve, and
reuse it across implementations.

## Why coding agents work better than most business agents

Coding agents are one of the clearest examples of LLM agents doing useful multi-step work. This is not
only because models generate code. It is because coding agents operate inside an environment that already
knows how to judge their work: compilers, type systems, automated tests, linters, static analysis,
runtime errors, debuggers, version control, code review, CI/CD, reproducible builds, and explicit
acceptance criteria.

The model proposes a change; the surrounding harness constrains, executes, challenges, and measures it,
then returns structured feedback. A coding agent can often tell whether the output compiles, whether tests
pass, what changed, where a failure occurred, whether it caused a regression, and whether the work
satisfies a defined requirement.

Most business agents lack an equivalent judgment environment. They may have documents, APIs, databases,
workflows, and retrieval, yet still struggle with questions such as:

- which evidence is applicable;
- which source takes precedence;
- which exception changes the rule;
- whether the conclusion is sufficiently supported;
- whether missing information is material;
- when confidence is too low;
- when the agent should stop;
- when the decision should be escalated;
- what makes the recommendation defensible;
- how the organization can test the agent before trusting it.

This is the gap Judgment Pack is designed to address. It is intended to supply part of the business-agent
equivalent of a compiler, a test suite, and a review harness. It does not execute every workflow itself.
It gives agent systems a structured contract describing what evidence matters, how judgment should be
applied, what outcomes are acceptable, and how behavior should be evaluated.

<div class="notice notice-info"><strong>In short.</strong> Coding agents have compilers and tests.
Business agents need explicit, testable judgment.</div>

Judgment Pack does not, on its own, solve AI reliability. It narrows one specific gap: making the judgment
behind a decision explicit enough to inspect and test.

## Knowledge helps an agent answer. Judgment helps it decide.

A knowledge source and a Judgment Pack serve different functions, and they are complementary.

| Knowledge source | Judgment Pack |
| --- | --- |
| Stores or retrieves information | Defines how information affects a decision |
| Finds semantically relevant content | Determines applicability |
| May provide citations | Connects evidence to the conclusion |
| Describes general rules | Represents conditions, rules, and exceptions |
| Helps generate an answer | Helps determine whether the answer is justified |
| Usually tested indirectly | Includes explicit behavioral evaluations |
| Often tied to one application | Designed to be portable across implementations |

## What Judgment Pack is not

Judgment Pack is not:

- an agent framework;
- an LLM;
- a workflow engine;
- a knowledge base;
- a vector database;
- a prompt format;
- a proprietary runtime;
- or only an evaluation dataset.

Judgment Pack is a portable contract describing the judgment an implementation is expected to apply and the
evidence needed to verify its behavior. The JSON schema is the serialization mechanism, not the whole
value: the value is the explicit, testable judgment the document carries.

## A concrete example: approving a supplier invoice

Consider a common operational decision: **Should this supplier invoice be approved automatically?**

**Decision.** Determine whether a supplier invoice may be approved automatically, escalated to a reviewer,
rejected, or held pending missing evidence.

**Evidence.** The decision may draw on evidence such as:

- a matching purchase order exists;
- the invoice amount is within the approved tolerance;
- goods receipt is complete;
- the supplier is not restricted;
- required tax information is present.

**Judgment logic.**

- approve automatically when all required evidence is present and the amount is within tolerance;
- escalate when the amount variance exceeds the automatic-approval threshold;
- abstain when required evidence is unavailable;
- reject when the supplier is restricted;
- record which evidence and rules produced the result.

**Evaluation cases.**

- exact match and complete receipt approve;
- small permitted variance approve;
- excessive variance escalate;
- missing receipt abstain;
- restricted supplier reject.

A small excerpt of the corresponding document illustrates the shape (fields omitted for brevity):

```json
{
  "specVersion": "0.1.0-draft",
  "decision": {
    "intent": "Determine whether a supplier invoice may be approved automatically.",
    "question": "Should this supplier invoice be approved automatically?"
  },
  "outcomes": [
    { "id": "approve", "label": "Approve automatically" },
    { "id": "escalate", "label": "Escalate to reviewer" },
    { "id": "reject", "label": "Reject" }
  ],
  "rules": [
    {
      "id": "restricted-supplier",
      "description": "Reject invoices from a restricted supplier.",
      "when": {
        "op": "fact",
        "path": "/supplier/restricted",
        "operator": "equals",
        "value": true
      },
      "outcome": "reject",
      "onUnknown": "escalate"
    },
    {
      "id": "excessive-variance",
      "description": "Escalate when the amount variance exceeds the automatic-approval threshold.",
      "when": {
        "op": "fact",
        "path": "/invoice/variance",
        "operator": "greater-than",
        "value": "0.05"
      },
      "outcome": "escalate",
      "onUnknown": "escalate"
    }
  ]
}
```

[See the complete supplier-invoice example](../examples/supplier-invoice-approval.json) for the full validated
document, including evidence requirements, sources, exceptions, escalation, and evaluation cases.

## Next steps

- Read the [specification](../spec/judgment-pack-core.md) for the normative model and conformance requirements.
- Follow development and open issues on
  [GitHub](https://github.com/Judgment-Pack/judgment-pack-spec).
- Join the discussion on [Slack](https://judgment-pack.slack.com).

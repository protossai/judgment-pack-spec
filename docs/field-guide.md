# The Judgment Pack field guide

This is a plain-language companion to the [normative specification](../spec/judgment-pack-core.md). It walks
through every part of a Judgment Pack document so that a newcomer can read one, write one, and understand what
each field means and does not mean. It is written for authors, reviewers, implementers, and anyone evaluating
whether the format fits their work.

<div class="notice notice-info"><strong>Informative.</strong> This guide is explanatory, not normative. The
authority is <a href="../spec/0.1.0-draft/">the normative specification</a> and its machine-readable
schema. Where this guide and the normative prose appear to disagree, the prose wins; where the prose and the
schema appear to disagree, the prose still wins and the mismatch is a specification defect that should be
reported.</div>

## What the name means

A **Judgment Pack** is a portable document that describes a decision: the question being asked, the evidence
that may bear on it, the possible results, the rules and exceptions that connect evidence to a result, and how
uncertain or out-of-scope situations are handed off. It is a self-contained JSON file that can be inspected,
versioned, and moved between systems.

A few terms recur throughout:

- **Core** is the base representation described here. It deliberately excludes profiles and extensions that a
  later specification may layer on top.
- **`0.1.0-draft`** is the exact experimental draft this guide describes. It is a research preview and may
  change in incompatible ways.
- **JPS** is shorthand for the Judgment Pack Specification, the body of normative prose and schema that defines
  the format.

The single most important framing: this draft standardizes **documents**, not decision execution. It defines
what a valid Judgment Pack document looks like and what its fields mean. It does not define a portable engine
that reads a pack and returns a decision.

## Normative words

The specification uses the requirement keywords from BCP 14, and they carry their special meaning **only when
written in all capitals**:

| Keyword | Meaning |
| --- | --- |
| **MUST** / **REQUIRED** | An absolute requirement. |
| **MUST NOT** | An absolute prohibition. |
| **SHOULD** | Recommended; deviating requires a considered reason. |
| **SHOULD NOT** | Normally prohibited; doing it anyway requires a considered reason. |
| **MAY** | Genuinely optional. |

Lower-case "must", "should", or "may" in this guide are ordinary English, not requirements.

Several other terms have precise senses in JPS:

- **Normative** — text or schema that determines conformance. A conformance claim must satisfy it.
- **Informative** — text that explains or experiments. It never determines conformance.
- **Carrier** — the serialized JSON text itself, as bytes and characters.
- **Structural** — the permitted shape of the document and the permitted forms of each value.
- **Semantic** — identifiers, references, and cross-field relationships between values.
- **Evaluator** — software that reads a pack, applies its conditions to runtime facts, and produces a result.
- **Runtime facts** — the external input supplied at evaluation time, against which conditions are tested.
- **Handoff** — passing a case to a person, queue, or system when the pack escalates or does not resolve.

<div class="notice notice-info"><strong>Precedence.</strong> When the normative prose and the schema conflict,
the prose controls. The schema is a machine-readable projection of the structural rules, not an independent
authority.</div>

## The three conformance levels

This draft defines conformance for **documents** at three cumulative levels. Each level assumes the one before
it.

- **Carrier-conforming** — the file is complete, valid JSON per RFC 8259, has no duplicate member names within
  any object, and is not silently truncated. A reader that hits a resource limit must fail explicitly rather
  than process only the part it managed to read.
- **Structurally conforming** — the document also passes the JSON Schema, including the `format` assertions.
  In JPS these `format` checks are required, not advisory: `id` must be an absolute URI, `source.publishedAt`
  must be an RFC 3339 full date, and every `createdAt` / `reviewedAt` must be an RFC 3339 date-time.
- **Semantically conforming** — the document is also internally coherent: local ids are unique within their
  collections, every reference resolves to exactly one object of the correct kind, every declared required
  extension is satisfied, and the cross-field rules in the prose hold.

<div class="notice notice-warning"><strong>What conformance does not mean.</strong> None of these levels
establish that a claim is true, that evidence is authentic or sufficient, that an author or reviewer had
authority, that an outcome is legal or safe, or that any particular runtime applied the pack correctly.
Conformance is about document validity, nothing more.</div>

## JSON punctuation

A Judgment Pack is ordinary JSON. If the notation is unfamiliar, this is the whole vocabulary:

| Token | Role |
| --- | --- |
| `{ }` | An object: an unordered set of name/value members. |
| `[ ]` | An array: an ordered list of values. |
| `"text"` | A string. |
| `:` | Separates a member name from its value. |
| `,` | Separates members in an object or elements in an array. |
| `true` `false` | Boolean values. |
| `null` | The explicit null value. |
| `12` `-3.5` | A JSON number. |

Two practical notes on numbers. JSON numbers are discouraged for business quantities whose exact decimal
identity matters, because they can lose precision. And the operands of ordered comparisons in a condition are
**decimal strings**, such as `"5000"`, not JSON numbers — see [JSON Pointer and decimal
strings](#json-pointer-and-decimal-strings).

JSON has no comments, no trailing commas, and no `NaN` or `Infinity`; any of these makes the text invalid.
Member names within a single object must be unique.

## Root fields

The document is a single JSON object. These are its members:

| Field | Required | Meaning |
| --- | --- | --- |
| `specVersion` | yes | Exactly the string `0.1.0-draft`. |
| `id` | yes | A stable absolute URI identifying the pack series. |
| `version` | yes | The revision of this pack (see below). |
| `title` | yes | A non-empty human-readable title. |
| `description` | no | A human-readable overview. |
| `decision` | yes | The decision intent and question. |
| `applicability` | no | A condition delimiting when the pack applies. |
| `evidenceRequirements` | no | Declared inputs or proof obligations. |
| `sources` | no | Located source material. |
| `outcomes` | yes | Two or more possible results. |
| `rules` | yes | One or more rules. |
| `exceptions` | no | Typed exceptions to rules or to normal resolution. |
| `fallbackOutcome` | no | An outcome id used when no rule produces a candidate. |
| `escalation` | no | Handoff configuration; not itself an outcome. |
| `metadata` | no | Authorship, license, creation, and review information. |
| `extensions` | no | Namespaced extension values. |

No other member is allowed at the root; an unrecognized name makes the document invalid. Array order is
preserved for authoring and display, but it **never** sets rule priority.

### Version tokens

Two version fields describe different things and move independently:

- `specVersion` names **which specification** the document follows. In this draft it is always the exact string
  `0.1.0-draft`.
- `version` names **which revision of this pack** you are looking at. It matches
  `^(0|[1-9][0-9]*)\.(0|[1-9][0-9]*)\.(0|[1-9][0-9]*)$` — three dot-separated non-negative integers with no
  leading zeros.

| Valid `version` | Invalid `version` | Why invalid |
| --- | --- | --- |
| `0.1.0` | `01.0.0` | Leading zero in a component. |
| `1.0.12` | `1.2` | Only two components. |
| | `v1.2.3` | A `v` prefix is not part of the grammar. |

## Local ids and references

Objects inside a pack — outcomes, rules, evidence requirements, sources, exceptions — carry a **local id**.
Every id matches:

```text
^[a-z][a-z0-9]*(?:-[a-z0-9]+)*$
```

Read token by token: it starts with one lowercase letter (`[a-z]`); continues with any lowercase letters or
digits (`[a-z0-9]*`); then allows any number of hyphen-joined groups, where each group is a hyphen followed by
one or more lowercase letters or digits (`(?:-[a-z0-9]+)*`). In plain terms: lowercase, starts with a letter,
words joined by single hyphens, no leading or trailing hyphen, no underscores, no capitals.

| Valid | Invalid | Why invalid |
| --- | --- | --- |
| `approve` | `ManualReview` | Capital letters. |
| `rule1` | `-review` | Leading hyphen. |
| `manual-review` | `review-` | Trailing hyphen. |
| | `review_case` | Underscore is not allowed. |

Local ids are meaningful only inside a single pack version. They are not globally unique, and no meaning may be
inferred from how one is spelled: `manual-review` and `escalate` carry weight only because other fields point
at them, not because of the words. This draft has no imports and no remote references — every reference
resolves within the one document.

## Core objects

### Decision

| Field | Required | Meaning |
| --- | --- | --- |
| `intent` | yes | Why the organization makes this decision. |
| `question` | yes | The specific question the pack resolves. |
| `extensions` | no | Namespaced extension values. |

Both `intent` and `question` are non-empty human-readable strings. The decision object must not embed prompts
or executable host-language code — it describes a decision, it does not script one.

### Evidence requirements

Each element of `evidenceRequirements` declares an input the decision may depend on.

| Field | Required | Meaning |
| --- | --- | --- |
| `id` | yes | Local id for the requirement. |
| `description` | yes | What must be provided. |
| `required` | yes | Boolean: whether absence prevents normal resolution. |
| `kind` | no | One of the descriptive kinds below. |

The optional `kind` is one of:

| `kind` | Descriptive meaning |
| --- | --- |
| `document` | A document expected to be supplied. |
| `fact` | A discrete data value. |
| `measurement` | A measured quantity. |
| `attestation` | A statement asserted by a party. |

`kind` is descriptive only. It does not prove that evidence is authentic, sufficient, or actually supplied.

### Sources

Each element of `sources` records provenance the author supplies.

| Field | Required | Meaning |
| --- | --- | --- |
| `id` | yes | Local id for the source. |
| `title` | yes | Human-readable title. |
| `publisher` | no | Who published it. |
| `publishedAt` | no | Publication date, an RFC 3339 full date (e.g. `2026-01-15`). |
| `locator` | yes | Where the material is (see below). |
| `citation` | no | A claim-level pointer into the material (see below). |
| `rights` | no | Rights or licensing information. |

A **locator** is an object with a `kind` and a `value`:

| `locator.kind` | Meaning |
| --- | --- |
| `uri` | A URI locating the material. |
| `repository` | A repository reference. |
| `path` | A filesystem or logical path. |
| `other` | Any other locator form. |

A **citation**, when present, must contain both a `location` and an `excerpt`. A locator is not fetched during
validation and is not evidence that the source exists, that the excerpt is accurate, or that its license
permits a given use.

### Outcomes

`outcomes` lists the possible results, and there must be at least two.

| Field | Required | Meaning |
| --- | --- | --- |
| `id` | yes | Local id for the outcome. |
| `label` | yes | Human-readable label. |
| `description` | no | Longer human-readable description. |

An outcome is a **declared result**, not an authorization to perform any external action. Carrying it out is
outside Core.

### Rules

`rules` lists one or more rules, each connecting a condition to an outcome.

| Field | Required | Meaning |
| --- | --- | --- |
| `id` | yes | Local id for the rule. |
| `description` | yes | What the rule does. |
| `when` | yes | The condition (see [The condition language](#the-condition-language)). |
| `outcome` | yes | The declared outcome id this rule proposes. |
| `onUnknown` | yes | `ignore` or `escalate`. |
| `evidenceRequirementRefs` | no | Ids of evidence requirements this rule relies on. |
| `sourceRefs` | no | Ids of sources supporting this rule. |
| `rationale` | no | Human-readable justification. |

`onUnknown` records what the author intends when the condition cannot be determined:

- `ignore` — the rule supplies no candidate outcome, and it does not block resolution. Crucially, this does
  **not** turn an unknown result into false; it simply stands aside.
- `escalate` — an unknown result blocks resolution and asks for a handoff.

There is no rule-priority field, and array order carries no priority meaning.

### Exceptions

`exceptions` describes typed departures from the ordinary rules.

| Field | Required | Meaning |
| --- | --- | --- |
| `id` | yes | Local id for the exception. |
| `description` | yes | What the exception does. |
| `when` | yes | The condition that triggers it. |
| `effect` | yes | `suppress-rule`, `force-outcome`, or `escalate`. |
| `targetRule` | conditional | Required for `suppress-rule` only. |
| `outcome` | conditional | Required for `force-outcome` only. |
| `onUnknown` | yes | `ignore` or `escalate`. |
| `sourceRefs` | no | Ids of supporting sources. |

The `effect` chooses which of `targetRule` and `outcome` are allowed:

| `effect` | `targetRule` | `outcome` | Meaning |
| --- | --- | --- | --- |
| `suppress-rule` | required | forbidden | Disable the named rule. |
| `force-outcome` | forbidden | required | Impose the named outcome. |
| `escalate` | forbidden | forbidden | Request a handoff. |

### Escalation

The optional `escalation` object records where a case should be handed off and why.

| Field | Required | Meaning |
| --- | --- | --- |
| `triggers` | yes | A non-empty, duplicate-free set of trigger tokens (below). |
| `target` | yes | Who receives the handoff (below). |
| `message` | no | A human-readable note for the recipient. |

The trigger tokens are:

| Trigger | Meaning |
| --- | --- |
| `not-applicable` | The pack's applicability condition was not met. |
| `missing-required-evidence` | A required evidence requirement was absent. |
| `unknown` | A blocking condition could not be determined. |
| `conflict` | Competing outcomes could not be reconciled. |
| `no-match` | No rule produced a candidate and no fallback exists. |

The `target` object has a `kind` — `human-role`, `queue`, or `system` — and a `name`. The escalation object
records handoff **intent**, not delivery: it does not prove a handoff happened, and Core defines no delivery,
routing, or authorization behavior.

### Metadata

The optional `metadata` object carries self-asserted authorship and review information. None of its members are
individually required.

| Field | Required | Meaning |
| --- | --- | --- |
| `authors` | no | A non-empty, duplicate-free list of author names. |
| `createdAt` | no | Creation time, an RFC 3339 date-time. |
| `license` | no | A license expression. |
| `requiredExtensions` | no | Extension names a consumer must support to fully interpret the pack. |
| `reviews` | no | Review records (below). |

Each entry in `reviews` records a `reviewer`, a `reviewedAt` date-time, a `disposition` of `approved`,
`changes-requested`, or `rejected`, and an optional `note`.

<div class="notice notice-warning"><strong>Metadata is self-asserted.</strong> It is not signed and does not
prove authority. A name in <code>authors</code> or <code>reviews</code> is a claim by whoever wrote the
document, nothing more.</div>

## The condition language

A condition appears in `applicability`, in each rule's `when`, and in each exception's `when`. Every condition
is exactly **one of five shapes**, distinguished by its `op` member.

**Literal** — a fixed Boolean.

```json
{ "op": "literal", "value": true }
```

The `value` must be `true` or `false`.

**`all` / `any`** — logical combinations.

```json
{ "op": "all", "conditions": [ { "op": "literal", "value": true } ] }
```

The `conditions` array is non-empty and holds nested conditions. Use `all` for conjunction and `any` for
disjunction.

**`not`** — negation of a single nested condition.

```json
{ "op": "not", "condition": { "op": "literal", "value": false } }
```

**`fact`** — a comparison against a runtime-supplied facts document.

```json
{ "op": "fact", "path": "/expense/amount", "operator": "greater-than", "value": "5000" }
```

- `path` is an RFC 6901 JSON Pointer selecting a value from the facts document.
- `operator` is one of the operators below.
- `value` is the operand. For ordered comparisons it is a decimal string; for `in` it is a non-empty array;
  for equality it may be any JSON value.

| `operator` | Meaning |
| --- | --- |
| `equals` | The selected value equals the operand. |
| `not-equals` | The selected value does not equal the operand. |
| `greater-than` | The selected value is ordered strictly above the operand. |
| `greater-than-or-equal` | Ordered above or equal. |
| `less-than` | Ordered strictly below the operand. |
| `less-than-or-equal` | Ordered below or equal. |
| `in` | The selected value is a member of the operand array. |

Equality is **type-preserving**: the string `"1"`, the number `1`, and the Boolean `true` are all distinct, and
there is no coercion between JSON types. Array equality is order-sensitive; object equality ignores member
order. The ordered operators (`greater-than` and friends) have a valid representation in the schema, but this
draft does **not** define a portable ordering for their decimal-string operands — structural acceptance of an
ordered condition does not imply that any two evaluators would order the values the same way.

**`evidence-present`** — checks whether a declared evidence requirement was supplied.

```json
{ "op": "evidence-present", "evidenceRequirement": "receipt" }
```

The `evidenceRequirement` names a declared requirement's id.

## How a pack would be evaluated (informative, experimental)

<div class="notice notice-warning"><strong>Informative and experimental.</strong> JPS 0.1.0-draft defines no
evaluator conformance. The following describes an experimental model only; a conforming implementation cannot
claim evaluator conformance in this draft.</div>

Everything in this section is a design experiment recorded so that reviewers can compare notes on it. The
allowed shapes of conditions remain normative through the schema, but the results and the algorithm below do
not determine conformance and may change.

**Three-valued conditions.** In the experiment a condition produces `true`, `false`, or `unknown`.

| `all` | Result |
| --- | --- |
| Any child is `false` | `false` |
| Every child is `true` | `true` |
| Otherwise | `unknown` |

| `any` | Result |
| --- | --- |
| Any child is `true` | `true` |
| Every child is `false` | `false` |
| Otherwise | `unknown` |

| `not` (input) | Result |
| --- | --- |
| `true` | `false` |
| `false` | `true` |
| `unknown` | `unknown` |

**Evidence presence** is `true` when the runtime explicitly associates at least one supplied item with the
named requirement; `false` when a complete evidence manifest is available and associates none; and `unknown`
when the runtime cannot tell whether the manifest is complete. This draft defines no evidence-manifest
interchange format.

**Result and reason tokens.** The experiment distinguishes three result kinds: an `outcome` result naming
exactly one declared outcome; a `not-applicable` result (which is not an outcome); and an `unresolved` result
carrying one or more reasons. The reason vocabulary matches the escalation triggers — `not-applicable`,
`missing-required-evidence`, `unknown`, `conflict`, `no-match` — plus a separate `exception-escalation` reason
that marks a direct request from a true `escalate` exception rather than a trigger-selected one. Reasons form a
de-duplicated set with no priority order.

**Experimental evaluation order.** Roughly, an evaluator would:

1. Test **applicability** (an omitted `applicability` counts as `true`). If false, stop with `not-applicable`;
   if unknown, stop `unresolved` with reason `unknown`.
2. Inspect **required evidence**, recording `missing-required-evidence` for any that is absent.
3. Evaluate **exceptions** and combine their effects: `suppress-rule` effects union their targets;
   `force-outcome` effects agree only if they name the same outcome; `escalate` effects take precedence and
   record `exception-escalation`.
4. **Stop `unresolved`** if required evidence is missing, an exception is unknown with `onUnknown: escalate`,
   forced outcomes conflict, or an exception directly requests escalation.
5. Otherwise honor a single compatible **forced outcome**, or else evaluate the **unsuppressed rules**.
6. Collect the outcomes of **true rules** as candidates; an unknown rule with `onUnknown: escalate` records
   `unknown` and blocks, while `onUnknown: ignore` contributes nothing and does not block.
7. If true rules name **more than one** distinct outcome, record `conflict` and stop `unresolved`.
8. Return the **single candidate** if there is one; otherwise use `fallbackOutcome` if present; otherwise stop
   `unresolved` with reason `no-match`.

Array order and lexical id order never break a tie in this experiment. `onUnknown: escalate` blocks otherwise
compatible outcomes; `onUnknown: ignore` never rewrites an unknown into false and never erases it from a trace.

## JSON Pointer and decimal strings

A `fact.path` is a JSON Pointer matching:

```text
^(?:/(?:[^~/]|~0|~1)*)*$
```

- The empty string selects the **whole facts document**.
- Each `/` begins a new segment.
- `~1` means a literal `/` inside a segment, and `~0` means a literal `~`.
- An unescaped `~` (one not followed by `0` or `1`) is invalid.

For example, `/expense/amount` selects the `amount` member of the `expense` object.

The operand of an ordered comparison is a **decimal string** matching:

```text
^-?(?:0|[1-9][0-9]*)(?:\.[0-9]+)?$
```

| Valid | Invalid | Why invalid |
| --- | --- | --- |
| `"0"` | `"+1"` | Leading plus sign. |
| `"-0"` | `"01"` | Leading zero. |
| `"12"` | `"1."` | No digits after the decimal point. |
| `"-12.50"` | `".5"` | No integer part before the point. |
| | `"1e3"` | Exponent notation is not admitted. |
| | `"NaN"` | Not a decimal. |

These are **strings, not JSON numbers**. The draft defines only the lexical grammar; it does not define scale,
units, cross-unit conversion, or ordering for these values.

## Extensions

The `extensions` object maps reverse-domain names to arbitrary JSON values:

```json
{
  "extensions": {
    "com.example.review-policy": { "minReviewers": 2, "quorum": "unanimous" }
  }
}
```

Extension names follow a fixed pattern: the first segment is lowercase and alphanumeric and starts with a
letter (no hyphens); there is at least one period; each later segment starts with a lowercase letter and may
contain lowercase letters, digits, and hyphens; and no name may begin with the reserved prefix
`org.judgmentpack.`.

An **optional** extension must not change Core semantics. Consumers should preserve optional extensions when
they round-trip a document, but they may otherwise ignore them.

A **required** extension is any name listed in `metadata.requiredExtensions`. Every listed name must also
appear as a key in some `extensions` object in the document. A consumer that lacks a listed capability must
report the document as structurally readable but not fully interpretable; it must not silently ignore a
required extension.

## Reading the JSON Schema

The normative schema is written in JSON Schema Draft 2020-12. This vocabulary is enough to read it:

| Keyword | Meaning |
| --- | --- |
| `$schema` | The schema dialect in use. |
| `$id` | The schema's own identifier URI. |
| `$comment` | A note for readers; carries no constraint. |
| `$defs` | A block of reusable named subschemas. |
| `$ref` | A reference to a subschema in `$defs`. |
| `type` | The permitted JSON type. |
| `properties` | Named members and their subschemas. |
| `required` | Members that must be present. |
| `additionalProperties: false` | No members beyond those named are allowed. |
| `additionalProperties: true` | Extra members are allowed. |
| `propertyNames` | A pattern every member name must match. |
| `const` | A single required value. |
| `enum` | A closed list of allowed values. |
| `pattern` | A regular expression the string must match. |
| `format` | A named string format, asserted in JPS (`uri`, `date`, `date-time`). |
| `minLength` | Minimum string length. |
| `items` | The subschema for every array element. |
| `minItems` | Minimum array length. |
| `uniqueItems` | Array elements must be distinct. |
| `oneOf` | Exactly one listed subschema must match. |
| `allOf` | Every listed subschema must match. |
| `anyOf` | At least one listed subschema must match. |
| `if` / `then` | Conditional constraints. |
| `not` | The listed subschema must not match. |
| `"value": true` | Any JSON value is allowed here. |

<div class="notice notice-info"><strong>Subtle point.</strong> <code>uniqueItems</code> rejects two identical
whole objects, but it does not stop two <em>different</em> objects from sharing the same <code>id</code>.
Uniqueness of ids is a separate semantic requirement enforced by the prose, not by
<code>uniqueItems</code>.</div>

## What the draft leaves undefined

`0.1.0-draft` deliberately stops at document representation and validity. It does not define:

- evaluator conformance;
- a portable serialized decision-result format;
- portable ordered-decimal comparison;
- units or conversions between them;
- an evidence-manifest interchange format;
- authentication or signatures;
- the authority of an author or reviewer;
- imports or remote references;
- rule priority;
- automatic fetching of sources;
- authorization to execute an outcome; or
- delivery guarantees for escalation.

This boundary is the point, not an oversight. JPS defines a portable representation and a document-validity
model; it is not a policy-execution engine.

## Next

- Read the [normative specification](../spec/judgment-pack-core.md) for the authoritative model and
  conformance requirements.
- Browse the [schema field reference](../schema/) for the machine-readable structural constraints.
- Study a [worked example](../examples/supplier-invoice-approval.json) — a complete, validated document.

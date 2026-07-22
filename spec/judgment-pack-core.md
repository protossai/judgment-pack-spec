# Judgment Pack Core `0.1.0-draft`

## Status

This document is a research preview. It may change incompatibly and MUST NOT be represented as an
industry standard or as suitable, by conformance alone, for consequential decisions.

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHOULD**, **SHOULD NOT**, and **MAY** are to be
interpreted as described by BCP 14 when, and only when, they appear in all capitals.

## 1. Purpose

Judgment Pack Core defines a portable JSON document for representing:

- a decision intent and question;
- possible outcomes;
- evidence requirements;
- sources and claim-level citations;
- applicability conditions;
- rules and typed exceptions;
- explicit behavior for unknown information;
- escalation requirements; and
- basic authorship and review metadata.

The core defines representation and limited portable evaluation semantics. It does not establish
truth, authority, safety, or fitness for a deployment.

## 2. Normative carrier

The normative carrier is a JSON value as defined by RFC 8259 with these additional constraints:

- the root MUST be an object;
- object member names MUST be unique;
- numbers SHOULD NOT be used for business quantities whose exact decimal identity matters;
- decimal business quantities MUST be encoded as strings matching the decimal grammar in §2.1;
- implementations MUST reject data exceeding their documented resource limits rather than process
  only a silent prefix; and
- unrecognized members are invalid except inside an `extensions` object.

### 2.1 Decimal grammar

```text
decimal = [ "-" ] ( "0" / non-zero-digit *DIGIT ) [ "." 1*DIGIT ]
```

Exponent notation, leading plus signs, leading zeroes, `NaN`, and infinities are not admitted.
This draft defines syntax only. Units and cross-unit comparison are outside Core.

## 3. Conformance classes

This draft distinguishes:

### 3.1 Structurally conforming document

A document is structurally conforming when it satisfies the normative JSON Schema and the carrier
requirements that JSON Schema cannot express.

### 3.2 Semantically conforming document

A structurally conforming document is semantically conforming when:

- every local reference resolves exactly once;
- referenced object kinds are correct;
- outcome, rule, evidence-requirement, source, and exception identifiers are unique within their
  collections;
- every rule outcome and fallback outcome names a declared outcome;
- every rule evidence reference names a declared evidence requirement;
- every rule source reference names a declared source;
- every exception target names a declared rule when a target is present;
- required extension capabilities are declared; and
- condition evaluation follows §7 when an implementation claims evaluation support.

### 3.3 Evaluator conformance

Evaluator conformance is reserved for a later draft. A tool MUST NOT claim evaluator conformance
under `0.1.0-draft`.

### 3.4 Non-claims

Conformance MUST NOT be described as proof that:

- a claim is true;
- evidence is authentic or sufficient;
- an author or reviewer had authority;
- an outcome is legally or ethically permissible;
- a particular runtime applied the pack correctly; or
- use of the pack is safe.

## 4. Root object

| Member                 | Required | Meaning                                                 |
| ---------------------- | -------: | ------------------------------------------------------- |
| `specVersion`          |      yes | Exact value `0.1.0-draft`                               |
| `id`                   |      yes | Stable absolute URI identifying the pack series         |
| `version`              |      yes | Three-component semantic version for this pack revision |
| `title`                |      yes | Non-empty human-readable title                          |
| `description`          |       no | Human-readable overview                                 |
| `decision`             |      yes | Decision intent and question                            |
| `applicability`        |       no | Condition controlling whether the pack applies          |
| `evidenceRequirements` |       no | Declared inputs or proof obligations                    |
| `sources`              |       no | Located source material                                 |
| `outcomes`             |      yes | At least two possible outcomes                          |
| `rules`                |      yes | One or more rules                                       |
| `exceptions`           |       no | Typed exceptions to rules or normal resolution          |
| `fallbackOutcome`      |       no | Outcome used only when all rules are definitively false |
| `escalation`           |       no | Required handoff intent for unresolved states           |
| `metadata`             |       no | Authorship, license, creation, and review information   |
| `extensions`           |       no | Namespaced extension values                             |

Collection order is preserved for authoring and display but MUST NOT determine rule priority.

## 5. Identity and references

The pack `id` MUST be an absolute URI. Local object identifiers are non-empty ASCII strings matching
`^[a-z][a-z0-9]*(?:-[a-z0-9]+)*$`.

Local identifiers are scoped to the pack version. They MUST NOT be interpreted as globally unique.
Meaning MUST NOT be inferred from the spelling of an identifier.

Core `0.1.0-draft` has no imports or remote-reference resolution. All rule, outcome, source,
evidence-requirement, and exception references resolve within one document.

## 6. Core objects

### 6.1 Decision

`decision.intent` explains the organizational purpose. `decision.question` states the question the
pack is intended to resolve. Both are required human-readable strings.

The decision object MAY include namespaced extensions. It MUST NOT embed prompts or executable
host-language code.

### 6.2 Evidence requirement

An evidence requirement declares:

- `id` — local identity;
- `description` — what must be provided;
- `required` — whether absence prevents normal resolution; and
- optional `kind` — `document`, `fact`, `measurement`, or `attestation`.

The kind is descriptive in this draft. Products may acquire or authenticate evidence differently.

### 6.3 Source

A source contains:

- `id` and `title`;
- a typed `locator` with `kind` and `value`;
- optional publisher and publication date;
- optional `citation` containing a location and excerpt; and
- optional rights information.

A source record represents provenance supplied by the author. Core conformance does not verify that
the source exists, that the excerpt is accurate, or that its license permits a proposed use.

### 6.4 Outcome

An outcome has a local `id`, human-readable `label`, and optional `description`.

An outcome is a declared result, not an authorization to perform an external action. Execution of
an outcome is outside Core.

### 6.5 Rule

A rule declares:

- `id` and `description`;
- `when`, a condition;
- `outcome`, a declared outcome id;
- `onUnknown`, either `ignore` or `escalate`;
- optional evidence-requirement references;
- optional source references; and
- optional rationale.

Rule order MUST NOT be used as priority. If more than one true rule names different outcomes, the
result is a conflict and requires escalation. Rules that name the same outcome do not conflict.

`ignore` means an unknown rule does not contribute a candidate outcome. It does not convert the
condition to false in traces or audit information.

### 6.6 Exception

An exception declares a condition and one effect:

- `suppress-rule`, with `targetRule`;
- `force-outcome`, with `outcome`; or
- `escalate`.

Exceptions are evaluated before normal rule resolution. Multiple true exceptions with incompatible
effects produce a conflict. An exception with an unknown condition follows its required
`onUnknown` policy.

### 6.7 Escalation

Escalation describes required handoff intent. `triggers` is a non-empty set chosen from:

- `not-applicable`;
- `missing-required-evidence`;
- `unknown`;
- `conflict`; and
- `no-match`.

The target identifies a human role, queue, or external system by a display name. Core does not
define delivery, identity resolution, authorization, or service-level objectives.

### 6.8 Metadata

Metadata MAY carry authors, creation time, license expression, and review records. These are
author assertions. Signature and organizational-authority profiles may strengthen them later.

## 7. Conditions and three-valued results

Conditions produce one of `true`, `false`, or `unknown`.

Core admits the following nodes:

- `literal` — returns its Boolean value;
- `all` — strong three-valued conjunction;
- `any` — strong three-valued disjunction;
- `not` — negation preserving `unknown`;
- `fact` — compares a value selected by a JSON Pointer-like path in runtime-supplied facts; and
- `evidence-present` — tests whether a named evidence requirement has supplied evidence.

### 7.1 `all`

- `false` if any child is false;
- `true` if every child is true;
- `unknown` otherwise.

### 7.2 `any`

- `true` if any child is true;
- `false` if every child is false;
- `unknown` otherwise.

### 7.3 `not`

`true` becomes `false`, `false` becomes `true`, and `unknown` remains `unknown`.

### 7.4 Fact conditions

The admitted operators are:

- `equals`;
- `not-equals`;
- `greater-than`;
- `greater-than-or-equal`;
- `less-than`;
- `less-than-or-equal`; and
- `in`.

Missing paths, unsupported types, incomparable values, and failed unit interpretation yield
`unknown`. Implementations MUST NOT coerce strings, numbers, Booleans, or null into one another.

Ordered comparison of decimal strings is reserved for a later evaluator draft. A structural
validator accepts them but MUST NOT imply executable comparison support.

## 8. Resolution model

This section describes intended semantics for design review; evaluator conformance is not yet
available.

1. Evaluate applicability. False yields `not-applicable`; unknown requires escalation.
2. Check required evidence. Missing required evidence requires escalation.
3. Evaluate exceptions. A single compatible true effect applies; incompatible true effects require
   escalation; unknown follows `onUnknown`.
4. Evaluate rules.
5. If one unique outcome is named by true rules, return it.
6. If multiple outcomes are named, require escalation for conflict.
7. If a rule with `onUnknown: escalate` is unknown, require escalation.
8. If every rule is false or ignored-unknown and `fallbackOutcome` exists, return the fallback only
   when no unresolved required condition remains.
9. Otherwise require escalation for no match.

A runtime MUST NOT silently select a rule by array order, lexical id, or implementation-defined
priority.

## 9. Extensions

`extensions` is an object whose keys use reverse-domain naming, for example
`com.example.review-policy`. Values may be any JSON value.

An optional extension MUST NOT change Core semantics. Consumers preserve optional extensions when
round-tripping but may otherwise ignore them.

Required extension semantics are declared in `metadata.requiredExtensions`. A consumer that does
not support every required extension MUST report the document as structurally readable but not
fully interpretable. It MUST NOT silently ignore a required extension.

Names beginning with `org.judgmentpack.` are reserved for future specification-defined extensions.

## 10. Security and privacy considerations

Implementations must treat packs, sources, citations, extensions, and runtime facts as untrusted
input. They SHOULD define limits for document bytes, nesting depth, collection sizes, string sizes,
and evaluation work.

Implementations MUST NOT:

- execute code found in strings or extensions;
- fetch source locators during ordinary validation unless explicitly requested;
- treat a URL or publisher name as proof of authenticity;
- expose sensitive evidence merely because a pack references it;
- convert conformance into authorization; or
- continue after silently dropping malformed or unsupported required content.

## 11. Versioning

`specVersion` identifies this specification draft. `version` identifies the pack revision. They are
independent.

During `0.x`, any specification release may be breaking. A future stable specification must define
reader, writer, and semantic compatibility separately and supply machine-readable migration cases.

A published pack version SHOULD be immutable. Changed content SHOULD receive a new version.

## 12. Open questions

Before a candidate stable core, the project must resolve:

- whether portable rule evaluation belongs in Core or a separate profile;
- exact decimal, unit, date/time, and comparison semantics;
- the minimum provenance and lineage model;
- whether authority and evaluation bindings belong in optional profiles;
- content identity, canonicalization, and signatures;
- imports and content-addressed dependencies;
- profile and capability negotiation; and
- a machine-readable semantic diagnostic contract.

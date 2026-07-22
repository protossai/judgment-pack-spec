# JEP 0000: Judgment Enhancement Proposal process

- Status: Active
- Type: Process
- Created: 2026-07-22

## Summary

A Judgment Enhancement Proposal (JEP) is the design record for a material change to the Judgment
Pack Specification, its profiles, conformance model, or governance.

## Required sections

Every standards-track JEP should contain:

1. **Summary** — the proposed change in plain language.
2. **Problem** — the interoperability problem and affected users.
3. **Evidence** — real examples and known implementation experience.
4. **Specification** — exact proposed portable semantics.
5. **Alternatives** — including no change, extension, profile, and product-only behavior.
6. **Compatibility** — reader, writer, semantic, and migration effects.
7. **Security and privacy** — abuse, confusion, disclosure, and resource risks.
8. **Conformance** — positive, negative, boundary, and adversarial cases.
9. **Implementation** — at least two plausible independent implementations.
10. **Unresolved questions** — issues that must not be hidden by acceptance.

## Statuses

- `Draft`
- `Review`
- `Accepted`
- `Rejected`
- `Withdrawn`
- `Superseded`

Acceptance means the design is approved for the maturity named by the JEP. It does not automatically
make a feature stable.

## Review

Research-preview JEPs remain open for public comment for a reasonable period based on complexity.
Maintainers may request prototypes or conformance cases before disposition. A stable normative
feature should not be accepted without evidence from two independent implementations.

## Compatibility

During `0.x`, breaking proposals are allowed but must be labeled and accompanied by migration
guidance. After a stable release, its compatibility policy takes precedence over this draft process.

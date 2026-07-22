# Security policy

## Research-preview support

`0.x` drafts are not supported for consequential production use. The project does not currently
provide security or compatibility service-level guarantees.

## Reporting a vulnerability

Do not open a public issue for a vulnerability that could expose data, bypass validation, cause
resource exhaustion, execute untrusted content, forge provenance, or confuse conformance with
authorization.

Use GitHub's private vulnerability reporting or a private security advisory for this repository
once enabled. If that mechanism is unavailable, contact the Protoss AI organization privately and
include:

- affected draft or artifact;
- minimal reproduction;
- expected and actual behavior;
- likely impact;
- whether the issue is already public; and
- any suggested mitigation.

Do not include customer packs, credentials, or other sensitive data in a report.

## Security boundary

A conforming document is untrusted input. Implementations should bound bytes, nesting, collection
sizes, string lengths, reference work, and evaluation work. Validators must not fetch locators,
execute extension content, or grant operational authorization merely because a document conforms.

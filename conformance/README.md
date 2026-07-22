# Conformance seeds

This directory contains early structural cases for design feedback. It is not yet a complete or
normative conformance suite.

[`manifest.json`](manifest.json) records the expected structural result and the primary behavior
being exercised. Invalid cases may produce additional diagnostics in different validators during
the research preview.

A candidate stable suite will require:

- canonical case identifiers;
- exact diagnostic classes independent of implementation language;
- positive, negative, boundary, and resource-exhaustion cases;
- semantic reference and three-valued-condition cases;
- cross-implementation agreement; and
- a versioned compatibility policy.

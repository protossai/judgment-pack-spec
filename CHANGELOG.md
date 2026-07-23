# Changelog

All notable changes prepared for or included in Judgment Pack Specification previews are recorded
here.

## Unreleased

### Added

- A `NOTICE` file naming Brian Jin as the copyright holder, included in the release bundle. The
  repository previously identified no owner: `LICENSE` is the unmodified Apache-2.0 text, whose
  appendix is a template rather than a filled-in field. `LICENSE` is deliberately left
  byte-identical to the canonical text so automated license scanners continue to report a clean
  Apache-2.0 match.

## `0.1.0-draft` — 2026-07-22

Initial research preview.

### Added

- Core prose specification and Draft 2020-12 structural schema.
- Synthetic expense-approval, software-change, and records-disposition examples.
- A 47-case carrier, structural, semantic, and capability corpus with machine-readable
  expectations.
- A domain-authoring test exercise and focused feedback template.
- Automated repository checks for schema, examples, conformance metadata, links, and fixture drift.
- Public governance, contribution, security, and JEP processes.

### Compatibility

This is the first tagged draft. It makes no compatibility promise for later
`0.x` releases. Pack evaluation is experimental and has no evaluator-conformance class in this
release.

### Known limitations

- No CLI or validator is part of the normative specification release; implementations are separate
  and nonnormative.
- Conformance does not establish factual truth, authority, safety, or operational fitness.
- Runtime facts, evidence transport, result traces, and ordered business-value comparison are not
  portable evaluation contracts in this draft.

# Specification and implementation boundary

## Status and scope

This document is an informative architecture description. It is not part of the normative Judgment
Pack Specification (JPS), and it does not turn any implementation's interfaces into JPS interfaces.
Implementation and release decisions for tools remain independently governed.

The specification repository and the implementations that consume it are kept in separate
repositories. The specification repository owns only the normative prose, schemas, conformance
suite, and release artifacts. Tools and implementations live in their own repositories and consume
versioned, immutable specification releases. The specification does not contain, deploy, or release
any executable, and the availability of any tool or service never changes JPS authority.

## Why keep the repositories separate

The specification and its tools have different authority, governance, and release needs.

- A tool must not become normative merely because of who maintains it. The tagged prose and schema
  retain authority when an implementation disagrees with them.
- Specification releases should be small, reviewable, and immutable. Tool releases may respond more
  frequently to platform support, packaging, security, performance, and usability needs.
- The specification repository must remain usable by independent implementers without installing any
  particular tool, and without requiring vendor packages, services, accounts, or network access.
- Runtime dependencies and supply-chain risk stay outside the repository that publishes the
  interchange contract.

The split also makes independent implementation meaningful: the conformance corpus is published by
the specification project, and any implementation is one consumer of that corpus rather than its
source of truth.

## Specification repository responsibilities

The specification repository owns:

- normative prose and its normative references;
- versioned JSON Schemas and immutable schema identifiers;
- carrier, structural, and semantic conformance cases and their manifests;
- synthetic examples, testing guidance, release notes, and compatibility statements;
- integrity tests that detect drift among prose, schemas, examples, and conformance metadata; and
- the source and deployment configuration for a static specification site.

The static site should publish immutable views of tagged releases and clearly label any view built
from `main` as work in progress. Its rendered pages must not override the tagged source artifacts.
The site should not host a validator service, execute packs, fetch source locators, or require an
account to read the specification.

This repository should not own executable packaging, shell completion, plugin discovery, update
checks, telemetry, credentials, operating-system installers, or mutable service infrastructure.

## Implementation responsibilities

Implementations live in their own repositories and own the decisions needed to distribute them.
An implementation is nonnormative: passing its checks establishes only the document-conformance
layers it actually reports, never factual grounding, authorization, safety, or fitness.

A useful implementation of document conformance validates the carrier, structural schema, semantic
references, and any required extension capabilities it supports, and reports the exact JPS release
used. Core validation should be deterministic and offline by default. In particular, it should
reject duplicate JSON member names, assert required URI/date/date-time formats, enforce documented
resource limits, and avoid fetching source locators. Network access or external extension execution
should require a separate explicit action.

There should be no unqualified evaluate, decide, or execute behavior presented as JPS conformance
while JPS lacks an evaluator conformance class. Any experimental evaluator should be unmistakably
labeled as experimental, produce no JPS evaluator-conformance claim, and remain outside the default
validation path.

## Version compatibility

Tool versions and the specification version are independent. `specVersion` identifies the exact JPS
release targeted by a pack; a tool's own version identifies a tool release. During JPS `0.x`, an
implementation should compare the complete `specVersion` and must not infer compatibility from a
shared major or minor prefix. An implementation may support several exact JPS releases, but it
should publish a machine-readable compatibility matrix and return an unsupported-version result for
anything outside that matrix. An unsupported version is not the same as an invalid document.

Released builds should validate against immutable, tagged specification artifacts. They may bundle
schemas and corpus metadata for offline operation, provided the files retain their upstream
identifiers and are verified against recorded digests. They should never use the specification
repository's mutable `main` branch as the default validation authority. An implementation bug fix
must not silently redefine JPS semantics; a normative interpretation change requires the
corresponding specification process, release notes, and compatibility analysis.

## Cross-repository testing and release flow

The repositories should integrate through immutable artifacts rather than shared source ownership:

1. A specification change updates prose, schema, examples, and focused conformance cases together in
   the specification repository. Its own CI validates artifact integrity before merge.
2. Before a JPS release, an implementation's CI can run the version-pinned corpus at the exact
   specification commit or release candidate. Any independent implementation can run the same
   artifact.
3. The specification repository publishes an immutable tag, versioned schema URL, conformance
   bundle, checksums, release notes, and matching static-site release view.
4. An implementation pins that tag and recorded checksums, updates its compatibility matrix, and
   runs old and new supported corpora. Specification CI does not push directly to or release any
   implementation repository.
5. Each implementation is released on its own cadence. A JPS release does not automatically create a
   tool release.
6. Failures are classified before correction: normative ambiguity or artifact disagreement is fixed
   through the specification process, while implementation, packaging, and integration defects are
   fixed in the relevant implementation repository.

This flow permits one tool release to support multiple JPS versions and one JPS release to be tested
by multiple tools. Neither repository needs write access to the other's release process.

## Authority and safety boundary

Every implementation is nonnormative. Passing an implementation's validation establishes only the
document-conformance layers actually reported. It does not establish that:

- a claim or cited source is true;
- evidence is authentic or sufficient;
- an author, reviewer, plugin, or operator has organizational authority;
- a declared outcome is lawful, ethical, safe, or suitable;
- an experimental evaluator produced a portable result; or
- an external action should be performed.

JPS confers no certification or standards authority, and implementation documentation, diagnostics,
plugins, and static-site presentation cannot override normative tagged JPS artifacts. Any future
certification, hosted validation, registry, evaluator, or execution service would require its own
explicit design, governance, threat model, and authorization.

# Versioning and release policy

Judgment Pack Specification releases use Semantic Versioning identifiers with a leading `v` on
Git tags. Specification `0.1.0-draft` is identified by the exact tag `v0.1.0-draft`. Only a tag and
GitHub release published by the maintainers establish an immutable release; similarly named files
on the mutable `main` branch do not.

## Specification versions

`specVersion` identifies the specification release a Judgment Pack targets. Readers MUST compare
the complete value; they must not infer compatibility from a shared major or minor number during
the `0.x` research period.

Any `0.x` release may change reader, writer, or semantic behavior incompatibly. Release notes must
identify those effects and include migration guidance when an earlier pack can be transformed.

The `main` branch describes work in progress. It is not an immutable specification release and
must not be used as a reproducibility anchor.

## Pack versions

The root `version` member identifies a revision of one pack series. It is independent of
`specVersion` and follows the three-component grammar defined by the Core schema.

A published `(id, version)` pair SHOULD be immutable. Changed pack content should receive a new
pack version, even when `specVersion` does not change.

## Compatibility dimensions

Release notes describe compatibility separately for:

- **reader compatibility** — whether an older reader can parse and preserve a newer document;
- **writer compatibility** — whether a newer writer can emit documents accepted by older readers;
- **semantic compatibility** — whether conforming consumers assign the same portable meaning; and
- **migration compatibility** — whether a deterministic document transformation is available.

No compatibility dimension is implied merely because two releases share a version prefix.

## Release artifacts

An immutable preview release contains, at minimum:

- the tagged Core prose specification;
- the tagged JSON Schema with a versioned `$id`;
- examples and conformance cases from the same commit; and
- release notes identifying maturity, compatibility, and known limitations;
- `.tar.gz` and `.zip` artifact bundles from that commit; and
- SHA-256 checksums and GitHub build-provenance attestations for those bundles.

If prose, schema, or conformance artifacts disagree, the conflict is a specification defect. The
artifact roles and conformance boundaries in the tagged Core specification determine which claim
each artifact is permitted to make.

## Deprecation and support

Research-preview releases have no compatibility or security service-level guarantee. Maintainers
may deprecate or remove behavior in a later `0.x` release, but must record the change in release
notes. A stable release will require a separate support and deprecation policy before publication.

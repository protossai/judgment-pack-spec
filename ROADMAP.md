# Evidence-gated roadmap

Dates are intentionally absent. Progress depends on evidence, not calendar commitments.

## Stage 1 — Research preview

Current stage.

Goals:

- state the category thesis and specification boundary;
- publish a reduced core data model and example;
- gather criticism from standards engineers, domain experts, and implementers;
- identify concepts that should be removed before compatibility commitments exist.

Exit evidence:

- at least five substantive external design reviews;
- examples from three unrelated domains;
- documented resolution of major terminology and scope objections;
- no requirement for any particular product or service to interpret the examples.

## Stage 2 — Developer preview

Goals:

- publish a reference validator and machine-readable diagnostic format;
- expand positive, negative, boundary, and adversarial conformance cases;
- add a constrained YAML authoring profile if lossless conversion is proven;
- validate authoring ergonomics with independent developers.

Exit evidence:

- at least ten external authors produce structurally valid packs;
- one independent validator agrees on the complete corpus;
- breaking changes and migration behavior are documented;
- implementation complexity remains small enough for a second language.

## Stage 3 — Candidate specification

Goals:

- freeze a minimal compatibility boundary;
- separate core and optional profiles;
- publish security, versioning, extension, and conformance policies;
- create an external technical steering group.

Exit evidence:

- two independent validators in different languages;
- three independent production adopters;
- no unresolved disagreement on normative conformance outcomes;
- demonstrated export and import between competing implementations.

Only after these gates should the project consider a `1.0` standard, certification, a marketplace,
or neutral-foundation governance.

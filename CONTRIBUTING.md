# Contributing

Thank you for helping test whether organizational judgment can have a useful portable format.

This is a research preview. Critical feedback, counterexamples, and removal proposals are as
valuable as additive features.

## Good first contributions

- a pack example from a domain unlike the existing examples;
- an ambiguity or contradiction in the draft prose;
- a case accepted by the schema that should be rejected;
- a case rejected by the schema that should be portable;
- an interoperability, privacy, security, or authoring-cost concern;
- evidence that a concept belongs in a profile rather than Core.

## Before proposing a feature

Open a design issue describing:

1. the user or interoperability problem;
2. at least two realistic examples;
3. why an extension or external product behavior is insufficient;
4. compatibility and migration consequences;
5. security and privacy consequences; and
6. how two independent implementations could test agreement.

Material changes should become a Judgment Enhancement Proposal (JEP). Copy the structure in
[`jeps/0000-jep-process.md`](jeps/0000-jep-process.md).

## Pull requests

A pull request should:

- remain focused on one problem;
- link the relevant issue or JEP;
- update prose, schema, examples, and conformance cases together when applicable;
- include at least one negative or adversarial case for normative behavior;
- identify breaking changes explicitly; and
- avoid claiming that conformance proves truth, authorization, or safety.

Normative changes are not accepted solely because the reference implementation supports them.

## Developer Certificate of Origin

Contributions must be signed off using Git's `--signoff` option. The sign-off certifies the
Developer Certificate of Origin 1.1: <https://developercertificate.org/>.

## License

By contributing, you agree that your contribution is licensed under Apache License 2.0.

## Conduct

Participation is governed by [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md).

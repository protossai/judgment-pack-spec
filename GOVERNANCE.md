# Governance

## Current model: company-led research incubation

Judgment Pack Specification is currently stewarded by Protoss AI. This is an incubation model, not
a claim of vendor-neutral governance.

Protoss AI maintainers currently approve releases and normative changes. Technical discussion,
proposals, decisions, and compatibility analysis are expected to occur publicly in this repository.

Payment, employment, or commercial membership does not by itself establish the technical merit of
a proposal.

## Decision principles

Maintainers evaluate changes in this order:

1. demonstrated interoperability need;
2. safety and semantic clarity;
3. independent implementability;
4. compatibility and migration cost;
5. author and operator usability;
6. implementation complexity; and
7. ecosystem adoption potential.

Protoss product convenience is not sufficient reason to add a Core feature.

## Decision process

- Minor editorial changes may use ordinary pull requests.
- Material or normative changes require a JEP and public review period.
- Stable features should have two independent implementations and conformance cases.
- Maintainers publish the disposition and rationale for a JEP.
- Material objections and minority positions should be recorded rather than silently discarded.

During the research preview, maintainers may reject or remove concepts aggressively. There is no
compatibility promise for `0.x` drafts.

## Maintainer conflicts

A maintainer with a direct commercial or personal conflict should disclose it. Another maintainer
should lead disposition when practical.

## Path to neutral governance

The specification should move to multi-vendor governance only after all of these exist:

- two independent conforming validators;
- two language implementations not maintained solely by Protoss AI;
- at least three production adopters;
- at least one competing commercial implementation;
- sustained contributions from outside Protoss AI; and
- public compatibility, trademark, security, and certification policies.

Before those conditions, transferring the project to a foundation would add ceremony without
creating independent technical power.

When the conditions are met, the maintainers should publish a governance-transition JEP covering
the technical steering committee, elections, trademarks, intellectual property, release authority,
and the chosen neutral home.

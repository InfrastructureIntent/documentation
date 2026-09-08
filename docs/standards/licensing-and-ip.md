# Licensing and Intellectual Property Model

> **Status:** Working project policy
>
> This document records the intended InfrastructureIntent repository and licensing boundaries. It is project policy, not legal advice. Contributor-agreement language should be reviewed by qualified counsel before outside contributions are accepted.

## Goals

InfrastructureIntent is intended to support an open extension ecosystem while preserving control of the core Engine implementation and the option to build commercial products using project technology.

## Repository classes

### Documentation

The canonical public documentation repository is licensed under **Creative Commons Attribution 4.0 International (CC BY 4.0)** unless otherwise noted.

### InfrastructureIntent Engine

The concrete compiler/runtime Engine implementation is controlled project IP and is not intended to be released under the public ecosystem's permissive open-source license by default. Its repository should carry an explicit copyright/proprietary notice rather than an open-source license unless a future decision changes this policy.

### Public abstractions and SDK contracts

Public Engine extension contracts, Domain/Target abstractions, SDK surfaces, and similar independently consumable contracts are intended to use **Apache License 2.0**.

### Adapters, Integrations, Targets, Backends, and conformance tooling

Public ecosystem implementations are intended to use **Apache License 2.0** together with contributor terms that preserve InfrastructureIntent's ability to use accepted contributions in both open-source and commercial offerings.

### Conformance suites

Public conformance suites should be permissively licensed with the contracts they validate so third-party authors can demonstrate compatibility without access to proprietary Engine internals.

## Contributor rights

Before accepting external contributions, InfrastructureIntent should publish a contributor agreement that clearly grants rights sufficient to use and modify accepted contributions, distribute them as part of the open-source project, incorporate them into commercial products or services, and sublicense or relicense them where the agreement permits.

The exact legal text is intentionally not invented here and should be reviewed by qualified counsel. Until that agreement exists, external pull-request contributions are not accepted.

## Governance is separate from licensing

Open-source licensing does not transfer control of the official InfrastructureIntent project. Official repository ownership, architecture decisions, compatibility claims, release authority, package publication, roadmap, and brand governance remain project-governance concerns.

## Brand

Code and documentation licenses do not grant rights to present a fork or derivative work as an official InfrastructureIntent product, service, specification, or distribution. See `TRADEMARKS.md`.

## Future commercial products

Nothing in the intended open ecosystem prevents the project owner from creating commercial offerings that use the controlled Engine and/or permissively licensed ecosystem components, subject to applicable licenses and contributor terms.

## Repository-template requirement

Every InfrastructureIntent repository template should require an explicit repository license class. Repositories must not silently inherit a license based only on organization membership.

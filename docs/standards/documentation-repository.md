# Documentation Repository Standard

The `InfrastructureIntent/documentation` repository is the canonical source for organization-wide architecture, ADRs, terminology, governance, engineering standards, and cross-repository reporting.

Repository-local implementation notes, troubleshooting, and daily iteration logs remain with the implementation repository that owns the work.

## Canonical structure

```text
README.md
LICENSE
CONTRIBUTING.md
TRADEMARKS.md

docs/
  architecture/
    README.md
    glossary.md
    ADR-*.md
    scenarios/
  standards/
  weeklies/
```

## Architecture decisions

ADRs are canonical here. Implementation repositories may link to them and may keep repo-local implementation decisions, but must not fork or restate organization-wide architecture as an independent source of truth.

## Weekly reporting

Future weekly reporting should aggregate local iteration evidence from InfrastructureIntent implementation repositories rather than relying on one central agent's memory. The exact collector format and automation remain to be defined.

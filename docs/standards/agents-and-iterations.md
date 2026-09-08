# Agent and Iteration Standard

> **Status:** Draft foundation standard

Each implementation repository should provide local agent instructions and maintain local iteration evidence. The repository owns the execution record for its own work; cross-repository summaries are generated from those local records.

## Repository requirements

Each implementation repository should contain:

```text
AGENTS.md
docs/iterations/
```

`AGENTS.md` defines repository-specific operating rules, architecture boundaries, validation commands, documentation expectations, and local conventions needed by coding agents.

## Iteration log content

Iteration logs should capture, as applicable:

- what changed;
- decisions made;
- tests and evidence;
- related issues and pull requests;
- risks and blockers;
- next work;
- architecture implications or possible drift.

Iteration logs are evidence, not a second issue tracker.

## Cross-repository aggregation

The documentation repository will eventually aggregate iteration records and GitHub project data into weekly progress and architecture-drift reports. The collector format and automation remain to be designed.

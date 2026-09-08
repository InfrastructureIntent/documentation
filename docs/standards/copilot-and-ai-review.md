# Copilot and Architecture Review Standard

> **Status:** Draft foundation standard

InfrastructureIntent repositories will provide explicit GitHub Copilot/coding-agent instructions and eventually an advisory AI architecture-review workflow.

## Copilot / coding-agent instructions

Repository instructions should identify:

- canonical InfrastructureIntent architecture sources;
- repository ownership boundaries and prohibited dependencies;
- validation/test commands;
- deterministic-output expectations;
- diagnostic aggregation expectations;
- documentation and iteration-log obligations;
- contract generation/conformance rules where applicable.

## AI architecture reviewer

A future Azure-hosted model may review pull-request changes against canonical ADRs and standards and return structured findings such as pass, warning, or potential violation.

The AI review should initially be advisory. Rules that can be expressed deterministically should migrate into deterministic checks rather than depending on an LLM. The model is best suited to higher-level architectural drift that static tooling cannot express reliably.

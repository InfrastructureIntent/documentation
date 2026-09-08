# CI and Automation Standard

> **Status:** Draft foundation standard

InfrastructureIntent repositories should converge on reusable GitHub Actions workflows for pull-request/merge validation, deterministic build/test execution, package construction and validation, release publication, and architecture conformance.

## Initial principles

- tests run on pull requests and merges to `main`;
- packageable repositories prove packaging in normal CI;
- publication is separately gated from package construction;
- deterministic/static architecture checks should be preferred where rules can be encoded directly;
- AI architecture review begins as advisory and must not be the sole authority for merge decisions;
- Copilot and coding-agent instructions should point to canonical architecture rather than restating divergent versions of it.

Exact reusable workflow contracts will be defined while building the repository template.

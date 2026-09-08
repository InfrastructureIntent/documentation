# Repository Template Standard

> **Status:** Draft foundation standard

The `InfrastructureIntent/repository-template` repository will define the default structure for new implementation repositories.

Expected baseline:

```text
.github/
  workflows/
  copilot-instructions.md

docs/
  iterations/

src/
tests/

AGENTS.md
README.md
Directory.Build.props
Directory.Packages.props
global.json
.editorconfig
```

The template should provide test/build/package validation workflows, contributor/agent guidance, repository-local iteration structure, explicit licensing placeholders, and hooks for architecture checks.

Not every repository must contain .NET projects, so language/tooling-specific files may be removed when inappropriate; the ownership, CI, agent, licensing, and documentation standards remain applicable.

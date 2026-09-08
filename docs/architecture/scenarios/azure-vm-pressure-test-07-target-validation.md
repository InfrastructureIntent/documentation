# Azure VM Pressure Test 07: Target Validation Boundaries

> **Status:** Worked architecture pressure test
>
> This document tests how a semantically valid Azure Resource Graph can still fail for a selected deployment Target without leaking Target-specific rules back into Engine or Integration semantics.

## Context scope

`CompilationContext` is scoped to one Intent/compilation.

Different Intent documents may carry different context values, such as subscription/account selection, location defaults, credentials/profile selection, naming context, workflow metadata, or Target selection.

Context is not global Engine state and is not a substitute for resource or graph semantics.

## Question

What happens when Azure Intent is semantically valid, the Resource Graph is valid, and Backend lowering succeeds conceptually, but the chosen Target cannot represent the requested infrastructure or produces an invalid Target model?

The architecture should preserve ownership:

```text
Integration
    validates Azure domain correctness

Engine
    validates graph correctness

Backend
    maps Azure semantics into Target abstractions

Target
    validates Target-model correctness and emission requirements
```

## Scenario

Assume Azure Integration successfully materializes and validates:

```text
Managed VM:web-02
Managed NIC:web-02-nic
Managed Disk:web-02-data
Existing Subnet:network-rg/prod-vnet/app
```

Engine successfully resolves references and constructs a valid graph:

```text
Existing Subnet -> Managed NIC -> Managed VM
Managed Disk -------------------> Managed VM
Managed compute-rg -------------> Managed VM
```

At this point the infrastructure semantics are valid.

## Case 1: Target model validation failure

Suppose `Azure.Backend.Terraform` lowers the graph into Terraform Target Abstractions, but the resulting Target model violates a Terraform Target contract rule.

Expected ownership:

```text
Terraform Target validation
    -> structured Target diagnostic
    -> compilation fails before emission
```

Azure Integration should not learn Terraform syntax merely to prevent this error. Engine should not validate Terraform expressions.

## Case 2: valid Azure semantic feature unsupported by Target

A more important case is when the Azure domain model is valid but the selected Target contract cannot represent a supported Azure semantic feature.

This is not an Integration validation failure because the infrastructure itself is valid, and it is not an Engine graph failure because the graph is valid.

Expected ownership:

```text
Backend capability/conformance validation
    -> unsupported Target capability diagnostic
```

The Backend is the component that knows both Domain Abstractions and Target Abstractions and therefore owns representability.

## Case 3: existing-resource representation differs by Target

An existing subnet may remain semantically:

```text
IExistingResource + ISubnet
    Identity = network-rg/prod-vnet/app
```

The graph relationship is unchanged:

```text
NIC --attached-to--> Subnet
```

Different Backends may lower the same existing participant differently for Terraform, Bicep, ARM, or another Target.

If one Target contract cannot represent the existing participant with the semantic information available, that Backend reports an unsupported or insufficient-information diagnostic.

Engine does not change the graph relationship to accommodate the Target.

## Case 4: Backend produces incomplete Target model

If a Backend forgets to carry a required domain semantic fact into the Target model, that is a Backend defect or conformance failure, not a reason to expose Parsed Intent downstream.

A Backend conformance suite should detect this using a prepared Resource Graph.

## Case 5: Target emission failure

If the Target model is valid but the emitter cannot serialize it because of an implementation defect or unexpected runtime condition, Target emission owns the failure.

Expected user-correctable Target validation failures should be structured diagnostics and aggregated where safe. Unexpected implementation failures may use exceptions because they are not ordinary validation flow.

## Validation aggregation

If a Backend can determine that several independent domain features are unsupported by the selected Target, it should report all safely discoverable unsupported-capability diagnostics in one pass rather than stopping at the first.

Likewise, Target validation should aggregate independent Target-model errors when safe.

> Fail fast on unrecoverable structural conditions; aggregate independent user-correctable failures whenever safe.

## CompilationContext and Target selection

The selected Target may be part of the current compilation context, but context is per Intent/compilation.

```text
Intent A
    CompilationContext
        subscription = A
        location = eastus
        target = terraform

Intent B
    CompilationContext
        subscription = B
        location = westus2
        target = bicep
```

Engine must not cache or assume these values globally across compilations.

A Backend may receive the current compilation's context when needed, but context remains a defined contract and must not contain raw source Intent as an escape hatch.

## Result

The architecture survives the Target-validation pressure test with a four-layer validation model:

```text
Integration validation
    Is this valid infrastructure-domain intent?

Engine validation
    Is this a valid resolved Resource Graph?

Backend capability/conformance validation
    Can this valid domain graph be represented by this Target contract generation?

Target validation
    Is the produced Target model valid for this deployment technology?
```

Target emission follows only after those validations succeed.

The key rule is:

> A valid infrastructure Resource Graph does not imply that every Target can represent it. Target representability is a Backend/Target concern, not an Integration or Engine concern.

## Conformance implication

Backend conformance suites should prove supported resources lower successfully, unsupported features produce stable diagnostics, existing resources are lowered according to Target capability, no raw/Parsed Intent dependency exists, required semantic properties survive lowering, and validation failures are deterministic and aggregated where safe.

Target conformance suites should prove Target models are validated independently of infrastructure-domain semantics, valid models emit deterministic artifacts, invalid models produce stable diagnostics, and expected validation errors do not rely on exceptions as ordinary control flow.

## Open questions

- Where exactly is the boundary between Backend capability validation and Target model validation?
- How does a Backend declare which resource types/features it supports for a Target generation?
- Should capability support be queryable before full lowering so tooling can warn earlier?
- How are Backend and Target diagnostics normalized into the common compilation diagnostic contract?
- Does Target selection belong in `CompilationContext`, request metadata outside it, or both?
# Azure VM Pressure Test 06: Lossless IR and Backend Handoff

> **Status:** Worked architecture pressure test
>
> This document tests whether the resolved Infrastructure IR / Resource Graph contains enough semantic information for multiple Backends to lower the same Azure intent independently without reopening YAML or Parsed Intent.

## Question

Can two Backends consume the same resolved Azure Resource Graph and produce different Target models without depending on source syntax, Adapter behavior, or missing domain data?

The architecture requires the Resource Graph to be lossless for supported Backend needs.

## Scenario

Use a mixed Azure graph:

```text
Existing
    IExistingResource + IResourceGroup : network-rg
    IExistingResource + IVirtualNetwork: network-rg/prod-vnet
    IExistingResource + ISubnet        : network-rg/prod-vnet/app

Managed
    IManagedResource + IResourceGroup  : compute-rg
    IManagedResource + INetworkInterface: compute-rg/web-02-nic
    IManagedResource + IVirtualMachine : compute-rg/web-02
    IManagedResource + IManagedDisk    : compute-rg/web-02-data
```

The VM uses the NIC and disk. The NIC attaches to the existing subnet.

## Required semantic state before Backend handoff

After semantic analysis and graph construction, the Backend should have access to the resolved domain state it needs, including the VM's accepted semantic configuration, NIC/subnet references, disk semantics, existing-resource identity/type, relationships, dependencies, and lifecycle information.

## Backend A: Azure -> Terraform

`Azure.Backend.Terraform` consumes Azure Domain Abstractions plus Terraform Target Abstractions.

The Backend can lower managed resources into Terraform-managed constructs and existing resources into Terraform-native references according to Target capability.

The Backend must not need to read YAML or Parsed Intent to rediscover VM size, VM-to-NIC relationship, NIC-to-subnet relationship, disk attachment, scope, lifecycle, or canonical identities.

## Backend B: Azure -> Bicep

`Azure.Backend.Bicep` consumes the exact same Azure Resource Graph but lowers it into Bicep Target Abstractions.

Again, the Backend must not return to YAML or Parsed Intent.

## Core pressure-test invariant

If either Backend needs to ask:

```text
"What did the original YAML say for this field?"
```

then the Infrastructure IR is lossy.

Likewise, if a Backend needs to infer relationships by re-parsing names or source structure, semantic analysis has failed to preserve required domain meaning.

The governing rule is:

> Every accepted semantic fact required by a conformant Backend must survive into the resolved domain resources, graph relationships, dependencies, or explicit compilation context.

## What should not survive merely for convenience

Lossless does not mean copying every source detail into the Resource Graph.

Source-only concerns such as YAML key ordering, comments, aliases, formatting, and Adapter-specific syntax need not become domain state unless required for diagnostics or provenance.

The IR preserves semantic information, not source representation.

## Compilation context versus resource state

Some information may belong to compilation context rather than individual resources, such as subscription/account context, region defaults, credential/profile selection, naming-policy context, Target selection, or workflow metadata.

A Backend may receive both:

```text
ResourceGraph
CompilationContext
```

without reopening Parsed Intent.

This is acceptable as long as the context is a defined Engine contract rather than an escape hatch containing arbitrary source data.

## Managed versus existing lowering

The lifecycle distinction survives Backend handoff through type, not source syntax.

```text
ISubnet + IManagedResource
    -> Backend lowers as managed Target resource

ISubnet + IExistingResource
    -> Backend lowers as existing/reference Target construct
```

The semantic relationship remains the same:

```text
NIC --attached-to--> ISubnet
```

The Backend chooses representation based on lifecycle contract plus Target capability.

## Dependency handling at Backend boundary

The Backend receives semantic dependencies and managed provisioning order from Engine. It should not reconstruct dependency ordering independently from source data.

The choice of Target syntax belongs downstream; the dependency fact itself belongs to the Resource Graph.

## Failure test: omitted semantic property

If a Backend can only recover an accepted domain property from Parsed Intent, the test fails.

Expected architectural correction:

```text
Add the semantic property to the appropriate Domain Abstraction
or place truly compilation-scoped information in CompilationContext.
```

Do not give the Backend access to raw Intent as a shortcut.

## Existing resource lacks enough semantic data

If a Target requires additional domain information for an existing resource, the correct response is not to reopen YAML. Instead the domain contract, Intent, future enrichment, or an unsupported-capability diagnostic must account for the requirement before or during Backend lowering.

## Backend conformance implication

A Backend conformance suite should include a test proving that lowering succeeds using only:

```text
Domain Abstractions
ResourceGraph
defined CompilationContext
Target Abstractions
```

with no Adapter or Parsed Intent dependency available.

This can be enforced structurally by package/reference boundaries and executable tests.

## Result

> Domain Abstractions and the Resource Graph must preserve every accepted semantic fact required by supported Backends, while source-specific representation details remain outside the IR.

The same resolved Azure graph can support multiple Backends because domain meaning is already materialized in typed resources, references are resolved by Engine, relationships/dependencies are explicit graph facts, managed/existing lifecycle is represented by type, Target-specific existing-resource mechanics remain downstream, and defined CompilationContext may carry compilation-scoped values without exposing raw Intent.

## Open questions

- What is the minimum `CompilationContext` contract?
- How should Backend conformance tests prove they do not depend on Adapter or Parsed Intent assemblies?
- How are unsupported Target capabilities reported when a valid domain resource cannot be represented by a particular Target?
- Should every Backend receive the full Resource Graph or a filtered Integration-specific graph view?
- What minimum semantic properties must existing-resource domain contracts expose for multi-Target lowering?
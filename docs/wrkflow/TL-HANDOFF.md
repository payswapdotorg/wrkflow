
# Final TL Handoff — Wrkflow

## Mission

You are the Tech Lead / Orchestrator for the complete Wrkflow implementation.

The repository is the sole implementation source of truth. Do not infer missing requirements from conversation history. Start from:

1. docs/wrkflow/SOURCE-OF-TRUTH.md
2. docs/wrkflow/ARCHITECTURE.md
3. docs/wrkflow/DOMAIN-CONTRACTS.md
4. docs/wrkflow/IMPLEMENTATION-ROADMAP.md
5. docs/wrkflow/ACCEPTANCE.md
6. existing root AGENTS.md, apps/zcode-cli/AGENTS.md, DESIGN.md, CONTEXT.md, architecture-policy.yaml.

## What the product is

Wrkflow is not an RPA recorder, a browser-agent optimization, or a full SaaS-cloning project.

It is a workflow/application-slice compiler.

The user may:

- demonstrate one workflow;
- point Wrkflow at a reference app and ask it to explore/discover workflows;
- combine workflows across multiple applications;
- compile reusable capabilities;
- run the resulting compiled workflow app without the reference SaaS/application.

The original application is a specimen/reference. It is never a hidden runtime dependency of a package that claims standalone execution.

## Non-negotiable product invariant

~~~text
REFERENCE APP
     |
     | learn/inspect/probe
     v
COMPILED WORKFLOW APP
     |
     | original app removed
     | network removed
     | original credentials removed
     v
DECLARED STANDALONE WORKFLOW STILL WORKS
~~~

If an implementation violates this, the implementation is wrong rather than the requirement being relaxed.

## Critical semantics

### 1. Workflow scope

Compile the smallest semantic closure required by the selected workflow(s).

Do not rebuild unrelated application features.

### 2. Application Genome

When the user asks to analyze an application broadly, create a versioned Genome and a Workflow Atlas. Exploration is bounded and must report coverage/frontier.

### 3. Capability expansion

A package can gain support for additional workflows beyond its origin. Show this explicitly.

### 4. Multi-application workflows

One workflow graph can span many applications. Compile the workflow as a whole.

### 5. World dependencies

Some work cannot be made standalone:

- current email state;
- live social media state;
- real payments;
- other current external-world interactions.

Represent these as explicit World Ports with live/snapshot/test modes.

### 6. Artifacts

Files are first-class compatibility boundaries. The compiled app must own serialization and validation when the workflow's output requires an ecosystem-compatible file.

### 7. Verification

Use differential testing whenever a reference implementation remains accessible. Counterexamples become permanent regression fixtures.

### 8. Marketplace

Users can share, publish and sell compiled workflow apps, bundles, capability packs and application genomes. Listings must disclose verification, portability, dependencies, limitations, creator/license and version.

## Current repository assessment

The repo is a clean public fork/mirror baseline of zai-org/ZCode, currently on main, with the existing ZCode engineering substrate intact.

Strong existing foundations verified in the repository include:

- root and CLI AGENTS.md with spec-first implementation rules;
- architecture policy/checker;
- typed tool registry and permissions;
- Browser Use / Playwright primitives;
- MCP/plugin system;
- dynamic-workflow compiler and sandbox runtime;
- workflow artifact/provenance infrastructure;
- desktop/Web/CLI surfaces;
- existing plugin-store/marketplace implementation primitives.

Important gaps found:

- no locked Wrkflow product architecture;
- no Application Genome contract;
- no Capability IR;
- no Workflow Atlas contract;
- no standalone compiled workflow-app package contract;
- no dependency/World-Port model;
- no differential verification contract for reconstructed applications;
- no marketplace model specific to compiled workflow apps.

The new docs/wrkflow documents close the product-specification gap. They intentionally do not replace ZCode's existing runtime infrastructure.

## Implementation strategy

### First

Lock managed module boundaries in architecture-policy.yaml.

Every new module needs:

- module.ts
- contract.ts
- contract.example.ts
- CONTRACT.md

where required by the repository architecture governance.

### Then

Implement three concurrent workstreams:

**Worker A — domain**

Owns pure schemas and IR under the new packages/wrkflow-* modules.

**Worker B — observation / archaeology**

Owns reference-app observation, active exploration, evidence and analysis providers under apps/zcode-cli/packages/{observation,archaeology,exploration}.

**Worker C — compiler / runtime / verification**

Owns compilation, standalone runtime, World Ports, artifact compatibility and verification.

The TL owns cross-workstream contracts and integration.

## Reuse existing ZCode

Do not fork the architecture into a second runtime.

Reuse:

- tool registry for capability projection where it fits;
- Browser Use for observation/fallback;
- dynamic-workflow compiler/runtime;
- plugin/MCP infrastructure;
- existing artifact/provenance infrastructure;
- existing desktop/Web/CLI transport and UI conventions.

Only introduce new infrastructure where the existing boundary cannot express the required semantic.

## Implementation order

~~~text
P0 architecture lock
       |
       +--> W1-A domain
       +--> W1-B observation/archaeology
       +--> W1-C compiler/runtime/verification
                |
                v
       golden vertical slice
                |
                v
        multi-app workflow
                |
                v
        explore mode/genome
                |
                v
        artifact compatibility
                |
                v
          marketplace
                |
                v
       repair + reuse + drift
                |
                v
          productization
~~~

## Required proof before calling the product usable

The TL must demonstrate all of the following:

1. one real reference web application workflow is observed;
2. the workflow is converted to semantic capabilities;
3. a standalone package is generated;
4. the reference application is removed/blocked;
5. the package still executes successfully;
6. an ecosystem-compatible artifact is produced;
7. another application can open that artifact when applicable;
8. a three-application workflow works;
9. one unavoidable live World Port is correctly surfaced rather than hidden;
10. explore mode produces a Genome + Workflow Atlas with measured coverage;
11. a package exposes more than its originating workflow only when verified;
12. a package can be published and discovered through the marketplace;
13. a differential mismatch produces a reproducible counterexample;
14. the package survives the standalone network-isolation test.

## Engineering gates

Before merge of any behavior change:

~~~text
spec updated
tests updated
pnpm typecheck
pnpm lint
pnpm architecture:check --changed
target-package tests
E2E where UI changed
~~~

Report actual command results. Never write "passed" when the command was not run.

## Architectural anti-patterns

Reject implementations that:

- make the reference SaaS a runtime dependency of standalone packages;
- implement only browser recordings and call them compiled apps;
- expose hundreds of low-level GUI actions to the model as the primary interface;
- clone a whole application when a workflow slice is sufficient;
- hide external dependencies;
- claim discovered capabilities without verification;
- duplicate the existing tool registry/workflow engine/artifact store;
- let UI become authoritative state;
- bypass architecture governance because the new domain is "experimental";
- store credentials in evidence;
- mix marketplace payments/entitlements into local workflow-domain logic.

## TL operating loop

For every work item:

~~~text
read spec
  -> inspect current source
  -> identify single owner
  -> lock contract
  -> implement
  -> add fixture/test
  -> run architecture gates
  -> record evidence
  -> update roadmap
~~~

Keep each worker inside disjoint module scopes. Resolve cross-module design decisions at the contract level before merging implementation.

## Completion state

The roadmap is complete only when the product demonstrates that the dominant execution path for a compiled workflow is semantic/local rather than GUI-driven, while GUI automation remains available as observation, verification and fallback.

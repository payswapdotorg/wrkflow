
# Wrkflow — Source of Truth

## Status

This document is the entry point for the Wrkflow product architecture. It supersedes informal chat descriptions when they conflict. The implementation must follow the documents linked below.

## Product

Wrkflow evolves the ZCode agent harness into a compiler for software workflows.

The product observes or explores reference applications, reconstructs the semantic behavior needed by workflows, discovers reusable capabilities, and compiles the minimum required application slice into a self-sufficient, agent-native workflow application.

The reference application is a specimen, not a runtime dependency.

A compiled workflow application must be able to execute every behavior inside its declared standalone scope without the original application being installed, logged in, reachable, or running.

## Core transformation

~~~text
Reference application(s)
        |
        | observe / inspect / probe
        v
Application Genome
        |
        +--> Capability Graph
        |
        +--> Workflow Atlas
        |
        v
Workflow / Application-Slice IR
        |
        v
Dependency elimination + implementation acquisition
        |
        v
Compiled Workflow Application
        |
        +--> local/standalone runtime
        +--> agent-native interface
        +--> optional human UI
        +--> compatible artifact serializers
        +--> explicit external World Ports
        |
        v
Verification + repair
~~~

## Two discovery modes

### Teach mode

The user demonstrates a workflow in a reference application. Wrkflow captures observations, infers semantic events, reconstructs the required capabilities, and compiles the workflow.

### Explore mode

The user points Wrkflow at a reference application. Wrkflow actively maps reachable states, capabilities, data objects, rules, artifacts, and workflow families within an explicit exploration scope/budget. It must report measured discovery coverage and uncertainty; it must never claim exhaustive coverage of an unbounded application.

## Non-negotiable principles

1. Workflow, not whole-app, is the default compilation unit.
2. The original SaaS/application is not in the execution path of a standalone package.
3. A workflow may span any number of applications.
4. Software dependencies and world dependencies are different and must be modeled separately.
5. Unavoidable external dependencies remain explicit through typed World Ports.
6. A mini-app may support more workflows than the workflow that created it.
7. Every supported capability must have a confidence/provenance status.
8. Unverified inferred capabilities must never be presented as verified.
9. Agent-facing APIs are semantic and compact; GUI automation is observation, verification, or fallback—not the primary steady-state interface.
10. Artifact compatibility is a first-class contract.
11. Verification is required before promoting a reconstructed capability as reusable.
12. Evidence and implementation are separate: observed facts must remain traceable independently of generated code.
13. Reuse mature open-source/specification implementations before synthesizing new implementations.
14. All external I/O is behind explicit ports/adapters and carries audit, permission, cancellation, timeout, retry and idempotency semantics.
15. The repository is the implementation handoff surface: TL and workers must not depend on this conversation for missing requirements.

## Artifact vocabulary

### Application Genome

A versioned model of the reference application's discoverable semantics: object ontology, capabilities, states, transitions, rules, calculations, permissions, artifacts, external dependencies, errors, fingerprints, evidence and coverage.

### Capability

A semantic operation with typed inputs/outputs, preconditions, effects, side effects, permissions, verification and evidence.

### Workflow

An object-centric graph of triggers, events, state transitions, data dependencies, branches, joins, application boundaries and outputs.

### Application Slice

The smallest implementation model containing the objects, rules, algorithms, capabilities, state and artifact semantics required by a workflow or workflow family.

### Compiled Workflow Application

A portable executable package produced from an application slice. It contains everything required for its declared standalone scope.

### World Port

A typed boundary for data or side effects that belong to the external world rather than to the reconstructed application logic. Examples: current Gmail inbox, live WhatsApp messages, current YouTube search, Stripe payment commit.

### Capability Passport

The user-facing trust/compatibility document for a compiled package: verified capabilities, additional discovered capabilities, unverified capabilities, source/reference applications, coverage, tests, portability, external dependencies and limitations.

## Compatibility

Do not define compatibility as byte-for-byte reproduction unless a target ecosystem requires it.

The default contract is semantic compatibility:

- target application can open the artifact;
- intended objects and relationships are reconstructed;
- relevant calculations/results match the declared equivalence tolerance;
- important fields survive round-trip;
- declared versions/features are honored;
- failures occur within the documented compatibility envelope.

## External dependency taxonomy

Every workflow edge must be classified as one of:

- bundled: reproduced locally;
- user-input: supplied by the user or another local artifact;
- snapshot: captured external state consumed offline;
- live-read: current external state must be read;
- external-side-effect: a real outside-world mutation must occur.

Never hide a live-read or external-side-effect behind the word standalone.

## Scope of reconstruction

Wrkflow reconstructs functional behavior required for declared workflows/interoperability, not proprietary source code, licensing controls, authentication bypasses, DRM, or unrelated portions of a product. Deep application/native analysis is permitted only in an authorized analysis context.

## Existing ZCode substrate to reuse

The current repository already provides:

- tool registration/contracts and permission metadata;
- browser-use/Playwright/Computer Use primitives;
- plugin and MCP infrastructure;
- dynamic-workflow analysis/compiler;
- dynamic-workflow sandbox/runtime;
- workflow artifacts and provenance;
- desktop/Web/CLI execution surfaces;
- existing plugin marketplace primitives.

Do not create a second agent loop, second tool registry, second workflow engine, or second artifact store when the existing subsystem can be extended through an explicit contract.

## Source-of-truth hierarchy

When documents disagree, use this order:

1. this document and linked locked contracts;
2. docs/wrkflow/ARCHITECTURE.md;
3. docs/wrkflow/DOMAIN-CONTRACTS.md;
4. docs/wrkflow/IMPLEMENTATION-ROADMAP.md;
5. docs/wrkflow/ACCEPTANCE.md;
6. current source and existing repository policy;
7. external research.

External research informs decisions but does not override a repository-level decision.

## Change control

Any implementation that changes a locked semantic boundary must update the relevant spec before code. Do not silently invent missing semantics in code. Use a short architecture decision record when a new product-level tradeoff is discovered.

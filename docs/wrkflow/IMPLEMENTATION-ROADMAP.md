
# Wrkflow — Implementation Roadmap

## Delivery model

The TL owns the roadmap and keeps this document current. Workers may implement only within their assigned workstream scope. A work item is complete only when code, tests, spec and verification evidence are present.

The TL must preserve small independent commits and avoid rebases by partitioning work by module/directory.

## Phase 0 — Repository preparation / architecture lock

### T00 — Lock product contracts

Owner: TL

Read and adopt:

- docs/wrkflow/SOURCE-OF-TRUTH.md
- docs/wrkflow/ARCHITECTURE.md
- docs/wrkflow/DOMAIN-CONTRACTS.md
- docs/wrkflow/ACCEPTANCE.md

Deliver:

- architecture decision record for any deviations;
- updated architecture-policy.yaml with managed module reservations;
- module contracts for newly introduced modules;
- dependency direction checked by the architecture checker.

Gate: no implementation starts against an undocumented semantic boundary.

## Phase 1 — Parallel foundations

### W1-A — Domain / IR foundation

Scope:

~~~text
packages/wrkflow-model/**
packages/wrkflow-evidence/**
packages/wrkflow-capability/**
~~~

Responsibilities:

- strict runtime schemas;
- evidence model;
- workflow event/graph model;
- capability model;
- genome/slice/manifest types;
- dependency and World Port contracts;
- serialization/versioning fixtures.

Must not implement UI, browser I/O, marketplace networking, or native analysis.

Exit:

- schemas validate;
- fixtures cover every status/dependency class;
- contracts are imported through public entrypoints only.

### W1-B — Observation / archaeology substrate

Scope:

~~~text
apps/zcode-cli/packages/observation/**
apps/zcode-cli/packages/archaeology/**
apps/zcode-cli/packages/exploration/**
~~~

Responsibilities:

- observation event normalization;
- browser/DOM/accessibility evidence capture;
- reference artifact capture;
- probe planning/execution ports;
- evidence ledger integration;
- application fingerprinting;
- archaeology provider abstraction;
- REA/Ghidra provider adapters where available;
- active exploration scheduler.

Reuse existing Browser Use and tool/permission infrastructure.

Must not define the canonical IR itself; consume W1-A contracts.

Exit:

- one web application can be observed;
- one user demonstration can be converted to normalized semantic candidates;
- authorized reference application can be actively explored within a bounded budget;
- raw evidence remains traceable.

### W1-C — Compiler / runtime / verification foundation

Scope:

~~~text
packages/wrkflow-compiler/**
packages/wrkflow-verify/**
packages/wrkflow-artifacts/**
apps/zcode-cli/packages/workflow-compiler-runtime/**
apps/zcode-cli/packages/workflow-app-runtime/**
apps/zcode-cli/packages/world-ports/**
~~~

Responsibilities:

- application-slice compilation;
- dependency elimination;
- implementation acquisition interfaces;
- standalone package assembly;
- deterministic runtime;
- World Port runtime;
- differential verification;
- artifact readers/writers;
- counterexample persistence.

Reuse existing dynamic-workflow compiler/runtime where semantics fit.

Exit:

- hand-authored slice can compile;
- compiled package runs with original app absent;
- verification can compare reference vs candidate;
- artifact round-trip works for at least one target format.

## Phase 2 — First vertical slice

### T01 — Web application teach mode

Implement a complete path:

~~~text
human demo
 -> observation
 -> semantic reconstruction
 -> handoff to compiler
 -> compiled mini-app
 -> offline run
 -> verified output
~~~

Target should be a workflow with a deterministic artifact, not a live payment/social workflow.

Recommended acceptance family:

- project/schedule calculation;
- invoice transformation;
- structured document/report generation.

### T02 — Multi-app workflow

Extend the same vertical slice across at least three applications.

Use:

~~~text
App A -> App B -> App C
~~~

with one local capability and one optional World Port.

Acceptance must prove the compiled workflow is one executable graph, not a chain of GUI automations.

## Phase 3 — Explore mode / Application Genome

### T03 — Application Explorer

Given a reference web app, discover:

- state graph;
- object ontology;
- capability candidates;
- workflow families;
- artifact boundaries;
- dependency boundaries.

Report measured coverage and frontier.

### T04 — Workflow Atlas

Build a browsable graph of discovered workflows.

Each workflow shows:

- observed/inferred/verified status;
- capabilities;
- dependencies;
- portability;
- confidence;
- evidence.

### T05 — Automated workflow synthesis

Compose candidate workflows from verified capabilities and state transitions.

Only promote after verification.

## Phase 4 — Standalone application quality

### T06 — Self-containment gate

Add a hard test harness that removes:

- original app;
- original credentials;
- network;

and executes all declared offline tests.

Any hidden network call is a failure.

### T07 — Artifact compatibility

Implement at least two target artifact families.

For each:

- parser;
- serializer;
- validation;
- round-trip;
- differential tests.

### T08 — Capability expansion disclosure

A package must show:

- original workflow;
- additional verified workflows/capabilities;
- inferred/unverified surface.

The UI cannot collapse these into one undifferentiated supports label.

## Phase 5 — Marketplace

### W2-A — Package registry

Own:

~~~text
packages/wrkflow-marketplace/**
existing plugin marketplace adapters as reusable infrastructure
~~~

Implement:

- listing schema;
- immutable versions;
- capability-aware search;
- package metadata;
- license/creator fields;
- dependency and portability display.

### W2-B — Distribution / install

Implement:

- package acquisition;
- integrity verification;
- local installation;
- version pinning;
- update/rollback;
- trust/provenance display.

### T09 — Marketplace UX

Expose:

- discovery;
- Capability Passport;
- verified metrics;
- install/use;
- publish/share/sell flow.

Do not reuse plugin marketplace semantics blindly: compiled workflow apps are executable packages with different isolation/verification requirements.

## Phase 6 — Learning / repair

### T10 — Counterexample-guided repair

Failed differential cases feed:

~~~text
evidence -> hypothesis -> IR revision -> recompile -> reverify
~~~

### T11 — Reference fingerprint health

Detect application-version drift and trigger health checks/revalidation.

### T12 — Capability graph reuse

Before re-archaeologizing a new workflow, query the registry/marketplace for reusable verified capabilities.

## Phase 7 — Productization

### T13 — Desktop + Web

Existing desktop/Web surfaces render the same domain facts.

Desktop may offer deep local observation/analysis. Web may use remote/local hosts according to existing ZCode delivery semantics.

### T14 — Package execution UX

Users can:

- ask the harness to run a workflow;
- open a workflow app;
- inspect capabilities;
- see external dependencies;
- choose offline/snapshot/live profile;
- inspect verification evidence.

### T15 — Benchmark suite

Maintain a fixed benchmark set comparing:

- direct browser/computer use;
- compiled workflow app.

Track:

- success;
- latency;
- model turns;
- tool calls;
- GUI actions;
- cost;
- compilation cost;
- break-even;
- repair latency.

## Concurrency rules

Three workers can proceed concurrently only when:

- W1-A owns pure contracts;
- W1-B owns observation/archaeology;
- W1-C owns compiler/runtime/verification.

All cross-workstream changes happen through public contracts. No worker edits another worker's module internals.

The TL alone changes:

- cross-module contracts;
- architecture-policy module declarations;
- roadmap/status;
- shared root docs.

## Definition of done for any work item

~~~text
spec updated
contracts updated
implementation complete
unit/fixture tests complete
integration test added where applicable
architecture check passes
lint/typecheck passes
evidence recorded
limitations recorded
~~~

No item is complete merely because the happy-path demo works.

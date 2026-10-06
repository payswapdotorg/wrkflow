
# Wrkflow — Acceptance and Verification

## Product-level acceptance

Wrkflow is successful when a user can provide a reference application and produce an executable workflow application that no longer depends on that reference application for its declared standalone scope.

## A. Teach mode

### A1 — Demonstrated workflow

Given:

- a supported web application;
- a user performing one workflow;
- authorized observation.

Wrkflow must:

1. capture structured evidence;
2. reconstruct object-centric semantic events;
3. identify required capabilities;
4. classify dependencies;
5. compile a standalone package;
6. run the package without the reference application;
7. produce the declared output.

### A2 — No GUI dependency after compilation

For all offline capabilities:

- reference application is uninstalled/absent;
- browser session is gone;
- network is blocked;
- original credentials are unavailable.

The package still completes.

### A3 — Correct disclosure

The UI must clearly distinguish:

- verified capabilities;
- additional verified capabilities;
- inferred capabilities;
- untested capabilities;
- live external dependencies.

## B. Explore mode

### B1 — Point at an application

The user can supply an authorized local/web application target.

Wrkflow produces:

- Application Genome;
- discovered capabilities;
- discovered workflow candidates;
- coverage metrics;
- unresolved frontier.

### B2 — Bounded exploration

The explorer obeys:

- action budget;
- time budget;
- safety/permission policy;
- domain/application scope.

It stops predictably and reports why.

### B3 — No false exhaustiveness

The UI never says all workflows without a bounded definition. It reports:

~~~text
discovered / modeled / verified / unresolved
~~~

## C. Multi-app workflows

### C1 — Three-app workflow

At least one acceptance scenario crosses three different applications.

The workflow must have:

- a single graph;
- explicit application boundaries;
- shared objects/data dependencies;
- one compiled execution plan.

### C2 — Mixed portability

At least one acceptance workflow contains:

- local reconstructed computation;
- one snapshot dependency;
- one live-read World Port.

The system must run correctly in both snapshot and live profiles.

## D. External side effects

### D1 — Stripe-like payment

The compiled workflow may reproduce all local preparation/calculation, but a real payment commit must be represented as an explicit external-side-effect dependency.

Offline execution must stop before the external commit with a structured dependency requirement, not silently fail.

### D2 — Email/social

A workflow that reads current Gmail/WhatsApp/YouTube state must report that live-read dependency when current state is required.

A captured snapshot must enable offline processing when the workflow semantics permit it.

## E. Capability expansion

### E1 — More than the originating workflow

A package created from workflow A may expose B/C if they are verified.

The UI must display:

~~~text
Original scope: A
Additional verified scope: B,C
Inferred scope: D
~~~

## F. Artifact compatibility

### F1 — Target-app opening

For at least one target application:

~~~text
compiled app
 -> compatible artifact
 -> target application opens artifact
~~~

The target app must not be needed to produce the artifact.

### F2 — Round trip

Where supported:

~~~text
compiled artifact -> target app -> save/export -> compiled parser
~~~

The semantic projection remains equivalent within the declared policy.

## G. Differential verification

For a target workflow family:

- run the same fixtures against reference and compiled implementation;
- compare normalized semantic results;
- generate counterexamples for mismatches;
- retain all promoted counterexamples as regression tests.

No capability is marked verified without passing its declared verification suite.

## H. Standalone gate

A package marked standalone=true must pass a hard isolation test:

~~~text
DNS/network denied
reference app absent
reference credentials absent
external services unavailable
run package
assert expected results/artifacts
~~~

The package must not attempt undeclared external access.

## I. Performance benchmark

The compiled path should materially outperform direct GUI automation on steady-state executions.

Minimum benchmark report:

- baseline GUI latency;
- compiled latency;
- baseline model turns;
- compiled model turns;
- baseline tool calls;
- compiled tool calls;
- baseline GUI actions;
- compiled GUI actions;
- compilation cost;
- break-even execution count.

## J. Security acceptance

The system must prove:

- credentials are never serialized into evidence;
- evidence is treated as untrusted input;
- World Ports are permission-gated;
- package integrity is verified;
- offline profile blocks network access;
- reference-analysis authorization is explicit.

## K. Marketplace acceptance

A published package must expose:

- immutable version;
- Capability Passport;
- portability;
- external dependencies;
- creator/license;
- verification metrics;
- package integrity hash.

The marketplace must reject malformed or unverifiable package manifests.

## Golden vertical slice

The TL should choose one deterministic workflow as the permanent golden fixture.

Recommended characteristics:

- web application;
- structured data;
- non-destructive;
- useful artifact output;
- deterministic enough for differential testing;
- small enough to compile quickly.

The golden path must remain runnable after every architecture migration.

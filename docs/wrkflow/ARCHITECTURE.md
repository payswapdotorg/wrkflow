
# Wrkflow — Architecture

## 1. Architectural objective

Turn software workflows into independent, agent-native applications.

The system should move the cost of dealing with human-oriented software interfaces from every execution into a one-time or occasional compilation process.

The steady-state path is:

agent -> semantic capability -> local compiled implementation -> result

The reference application remains useful for discovery, differential verification and repair, but is not required for an offline package.

## 2. System topology

~~~text
                                 USER
                                  |
                  +---------------+----------------+
                  |                                |
                  v                                v
             TEACH MODE                       EXPLORE MODE
                  |                                |
            demonstration                  reference application
                  |                                |
                  +---------------+----------------+
                                  |
                                  v
                         OBSERVATION FABRIC
                                  |
                                  v
                       EVIDENCE + EVENT MODEL
                                  |
                                  v
                       APPLICATION ARCHAEOLOGY
                                  |
                    +-------------+-------------+
                    |                           |
                    v                           v
             APPLICATION GENOME          WORKFLOW ATLAS
                    |                           |
                    +-------------+-------------+
                                  |
                                  v
                            CAPABILITY IR
                                  |
                                  v
                         APPLICATION SLICE IR
                                  |
                    +-------------+-------------+
                    |                           |
                    v                           v
           IMPLEMENTATION ACQUISITION    DEPENDENCY ANALYSIS
                    |                           |
                    +-------------+-------------+
                                  |
                                  v
                         WORKFLOW COMPILER
                                  |
                         +--------+--------+
                         |                 |
                         v                 v
                 STANDALONE CORE      WORLD PORTS
                         |                 |
                         +--------+--------+
                                  |
                                  v
                    COMPILED WORKFLOW APP
                                  |
              +-------------------+-------------------+
              |                   |                   |
              v                   v                   v
         Agent API            Human UI           Artifacts
                                  |
                                  v
                         DIFFERENTIAL VERIFIER
                                  |
                                  v
                         REPAIR / PROMOTION
~~~

## 3. Layer boundaries

### Layer A — Observation

Captures human/app interaction and environmental evidence.

Inputs may include:

- DOM/accessibility trees;
- browser semantic actions;
- screenshots/video;
- keyboard/mouse events;
- network metadata and request/response shapes when authorized;
- file creation/open/save/import/export;
- application/runtime traces;
- application binaries/source/bundles supplied for authorized analysis;
- timing and state deltas.

Observation is evidence collection. It must not decide final semantics.

### Layer B — Evidence

Canonical immutable evidence records.

Each record contains:

- source;
- observation timestamp;
- scope;
- subject/object references;
- raw or normalized payload;
- integrity hash;
- privacy classification;
- provenance;
- confidence;
- limitations.

Evidence must be redaction-aware and must not contain secrets by default.

### Layer C — Workflow understanding

Converts raw interaction into object-centric events and workflow graphs.

A workflow event should describe intended semantic change where evidence supports it:

~~~text
crm.contact.upsert(email,name,company)
~~~

rather than only:

~~~text
click(x,y) -> type(...) -> click(...)
~~~

Raw GUI steps remain attached as evidence/fallback recipes.

### Layer D — Application Genome / Archaeology

Reconstructs application semantics from the strongest available evidence source.

Acquisition precedence:

1. installed/local reusable implementation;
2. mature OSS library;
3. standard/schema/specification;
4. documented API/SDK;
5. user-provided source/reference;
6. behavioral probing;
7. runtime/client/native analysis;
8. generated implementation.

Ghidra/REA/Hopper are instruments, not the architecture.

For a web SaaS, remote server behavior is normally inferred from observed inputs/outputs and state effects rather than recovered from a local binary.

### Layer E — Capability IR

A typed, semantic, evidence-backed operation.

Canonical fields:

~~~text
id
domain
version
inputSchema
outputSchema
preconditions
effects
stateReads
stateWrites
objects
idempotency
consistency
sideEffectScope
requiredAuthScopes
implementationCandidates
verificationStrategy
confidence
provenance
limitations
~~~

A capability candidate may exist in multiple implementations. Do not collapse uncertainty prematurely.

### Layer F — Application Slice IR

The minimal closed set of application semantics needed to execute a selected workflow family.

It includes:

- object types and relationships;
- local state model;
- required capabilities;
- pure functions/calculations;
- business rules;
- validation;
- scheduling/state transitions;
- artifact parsers/serializers;
- required reference data;
- external World Ports;
- failure/recovery semantics;
- verification fixtures.

This is the principal compilation input.

### Layer G — Dependency elimination

Classify every dependency as application-internal or world-external.

The compiler must attempt to eliminate application dependencies:

~~~text
SaaS database -> local state
SaaS calculation -> local implementation
SaaS rules -> local rules
SaaS file serializer -> local serializer
SaaS identity -> local package identity
SaaS UI -> discard unless human UI is specifically required
~~~

World dependencies remain ports:

~~~text
current email -> live-read port
payment charge -> external-side-effect port
current social feed -> live-read port
~~~

### Layer H — Workflow compiler

Compiles the slice into:

- portable runtime;
- semantic agent interface;
- optional GUI projection;
- artifact reader/writer;
- verifier;
- package manifest;
- provenance/capability passport.

The agent-facing interface must expose compact semantic operations, not GUI micro-actions.

### Layer I — Standalone runtime

The runtime runs without the original application for capabilities classified as bundled, user-input or snapshot.

Offline execution must not silently attempt network access.

The runtime should support deterministic mode for verification and reproducibility.

### Layer J — World Ports

External reality is injected through explicit ports.

Every port declares:

- live/snapshot capability;
- permissions;
- side-effect scope;
- idempotency;
- timeout;
- cancellation;
- retry;
- audit;
- privacy boundary.

A workflow can run against a real port, a snapshot port, a replay port, or a test/mock port.

## 4. Execution profiles

Every compiled package declares one or more profiles:

### Offline

No network or original application required.

### Snapshot

External data was captured earlier; execution consumes the snapshot.

### Hybrid

Only declared World Ports are live.

### Live

External reads and/or side effects are connected.

### Reference

Original application is available for differential testing only.

## 5. Application exploration

Explore mode is an active learning system.

The explorer maintains:

- discovered states;
- unexplored transitions;
- capability hypotheses;
- object hypotheses;
- workflow candidates;
- information-gain scores;
- exploration budget;
- safety budget;
- stop criteria.

It must prioritize probes that maximize useful new semantic coverage rather than exhaustively clicking every pixel.

Stop criteria may include:

- budget exhausted;
- state/capability frontier converged;
- user scope satisfied;
- safety boundary reached;
- authentication/permission boundary reached;
- diminishing information gain.

The UI must show exploration coverage and unresolved frontier.

## 6. Workflow discovery

The system derives candidate workflows from:

1. observed human demonstrations;
2. known capability compositions;
3. state-transition graph paths;
4. object lifecycle patterns;
5. artifact import/export paths;
6. recurring action clusters.

Candidate statuses:

- observed;
- inferred;
- verified;
- deprecated.

An inferred workflow must not be presented as verified until its declared verification contract passes.

## 7. Cross-application workflows

Applications are nodes in the same workflow graph.

A workflow may contain:

~~~text
Gmail -> CRM -> Spreadsheet -> Stripe -> Slack
~~~

but compilation should produce one workflow graph and one runtime plan rather than force the agent to orchestrate five GUIs.

Where application semantics can be reconstructed, compile them into local capabilities.

Where the outside world must remain involved, leave only the minimal World Port boundary.

## 8. Capability composition

Expose capabilities hierarchically.

Composite:

~~~text
process_inbound_lead()
reconcile_invoice()
prepare_project_schedule()
~~~

Semantic:

~~~text
contact.find()
contact.upsert()
schedule.calculate()
~~~

Primitive:

~~~text
browser.navigate()
browser.snapshot()
http.request()
file.read()
~~~

The model normally operates at the composite or semantic level. Primitive tools remain implementation mechanisms.

## 9. Verification architecture

Every promotion path must support differential verification.

~~~text
same input
   |
   +--> reference app
   |
   +--> compiled app
            |
            v
     normalized observations
            |
            v
      equivalence checker
~~~

Compare the relevant semantic projection:

- objects;
- fields;
- relationships;
- calculations;
- generated artifacts;
- error categories;
- state transitions.

Do not require irrelevant UI equivalence.

Counterexamples become regression fixtures.

The compiler should support iterative repair:

~~~text
candidate -> counterexample -> revised IR -> recompile -> reverify
~~~

## 10. Artifact compatibility

Artifact formats are first-class.

A serializer declares:

- format/version;
- supported fields;
- unsupported fields;
- normalization;
- validation;
- round-trip behavior;
- target application compatibility.

For a target application such as Microsoft Project, the preferred path is:

~~~text
local schedule model
      |
      v
target-compatible artifact
      |
      v
target application can open it
~~~

The target application is not required during local computation.

## 11. Portability and capability reporting

Every compiled package has a Capability Passport.

It must distinguish:

- original workflow;
- verified capabilities;
- additional verified capabilities;
- inferred capabilities;
- untested capabilities;
- external dependencies;
- offline support;
- supported artifact formats;
- reference versions;
- differential test count;
- known limitations.

The product must never advertise a package as fully standalone when a required World Port is live.

## 12. Marketplace

Marketplace entities:

- workflow app;
- workflow bundle;
- capability pack;
- application genome;
- adapter/world-port pack.

A listing must carry machine-readable manifest data plus human-readable presentation.

Minimum trust data:

- creator;
- version;
- license;
- source/reference declaration;
- capability passport;
- verification evidence;
- compatibility;
- dependencies;
- permissions;
- price model;
- update history;
- known limitations.

Marketplace packages are immutable by version. Updates create a new version.

Marketplace search should be capability-aware so compilation can reuse existing verified implementations.

## 13. Security and authorization

Reference analysis is permitted only in an authorized context.

Do not implement:

- credential extraction;
- DRM bypass;
- authentication bypass;
- licensing circumvention;
- hidden access to paid server-side functionality;
- copying unrelated proprietary product surface.

Evidence is untrusted input and cannot become model instructions merely because it appears in a webpage/app.

Secrets must stay in credential/identity adapters.

Compiled offline packages must have an explicit network policy; offline means network denied.

## 14. Data model ownership

Single owners:

- Evidence Store owns evidence;
- Genome Store owns application genomes;
- Capability Registry owns promoted capabilities;
- Workflow Registry owns workflow definitions;
- Package Store owns compiled package metadata/artifacts;
- Verification Store owns test/equivalence results;
- Marketplace service owns listing metadata;
- World Port adapter owns external credentials and external I/O;
- runtime owns transient execution state.

No UI component is an authoritative owner of these facts.

## 15. Suggested repository modules

The eventual implementation should prefer these boundaries.

Pure/reusable top-level modules:

~~~text
packages/wrkflow-model
packages/wrkflow-evidence
packages/wrkflow-capability
packages/wrkflow-compiler
packages/wrkflow-verify
packages/wrkflow-artifacts
~~~

Agent/host execution modules under apps/zcode-cli/packages:

~~~text
observation
archaeology
exploration
workflow-compiler-runtime
workflow-app-runtime
world-ports
marketplace
~~~

UI integrations remain in existing packages/ui, packages/web, packages/desktop through contracts.

These are planned boundaries; the TL must register them in architecture-policy.yaml as managed modules before adding source.

## 16. Reuse rules for current ZCode

Extend rather than replace:

- apps/zcode-cli/packages/core/src/tool/registry.ts -> semantic capability registration/projection only where appropriate;
- apps/zcode-cli/packages/browser-use-plugin -> observation and GUI fallback;
- apps/zcode-cli/packages/dynamic-workflow -> workflow compilation/analysis substrate;
- apps/zcode-cli/packages/dynamic-workflow-runtime -> sandboxed execution substrate;
- apps/zcode-cli/packages/adapters/src/plugins and existing UI/plugin store -> marketplace primitives where semantics fit;
- existing artifact/provenance infrastructure -> workflow package artifacts and evidence references.

Do not make the compiled runtime depend directly on the full agent/UI stack.

## 17. Determinism

Compiled workflow packages should support an injected execution context containing:

- clock;
- random source;
- locale;
- timezone;
- filesystem;
- network/world ports;
- identity;
- package version.

Verification runs should use deterministic providers.

## 18. Performance objective

Steady-state execution should be materially cheaper and faster than GUI automation for the same workflow.

The benchmark suite must record:

- total latency;
- model turns;
- tool calls;
- GUI actions;
- token usage;
- external calls;
- package warm-up;
- compilation cost;
- break-even execution count;
- repair latency.

## 19. Product success criterion

~~~text
reference application available once
        ->
workflow/application slice reconstructed
        ->
standalone compiled app
        ->
reference application removed
        ->
same declared business result still produced
~~~

That is the primary architecture invariant.

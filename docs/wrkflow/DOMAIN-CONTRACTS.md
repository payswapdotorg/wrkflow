
# Wrkflow — Domain Contracts

This document defines the canonical contracts the TL must preserve while implementing. TypeScript types and runtime schemas are implementation details of these contracts.

## 1. Evidence

~~~text
EvidenceRecord
  id
  sourceKind
  sourceIdentity
  scope
  observedAt
  subjectRefs[]
  payload
  payloadHash
  privacyClass
  provenance
  confidence
  limitations[]
~~~

### sourceKind

Examples:

~~~text
ui-event
dom
accessibility
screenshot
video
network-observation
file-artifact
runtime-trace
source-inspection
binary-analysis
documentation
user-assertion
generated-hypothesis
~~~

Evidence is append-only at the semantic level. Corrections produce a new record referencing the previous record; do not silently mutate historical evidence.

## 2. Object-centric workflow event

~~~text
WorkflowEvent
  id
  workflowId
  timestamp
  applicationRef?
  actorRef?
  operation
  input
  output?
  objectRefs[]
  stateBeforeRef?
  stateAfterRef?
  evidenceRefs[]
  confidence
~~~

The operation should be semantic whenever evidence supports semantic interpretation. Raw GUI steps may be attached as an execution recipe.

## 3. Workflow

~~~text
Workflow
  id
  name
  description
  version
  trigger[]
  objects[]
  nodes[]
  edges[]
  applications[]
  capabilities[]
  outputs[]
  constraints[]
  dependencyProfile
  discoveryStatus
  verificationStatus
~~~

The graph supports sequence, branch, join, loop/fan-out, data dependency, object dependency, application boundary, artifact boundary, and external/world boundary.

## 4. Capability

~~~text
Capability
  id
  name
  version

  inputSchema
  outputSchema

  preconditions[]
  effects[]
  stateReads[]
  stateWrites[]
  objectTypes[]

  idempotency
  consistency

  sideEffectScope
  requiredAuthScopes[]

  implementations[]
  verificationStrategy

  confidence
  evidenceRefs[]
  limitations[]
~~~

### Capability status

~~~text
candidate
inferred
verified
deprecated
~~~

A deprecated capability remains addressable for historical package versions but must not be selected for a new compilation unless explicitly pinned.

## 5. Application Genome

~~~text
ApplicationGenome
  id
  applicationIdentity
  referenceVersions[]
  fingerprint
  objectOntology[]
  states[]
  transitions[]
  capabilities[]
  rules[]
  calculations[]
  permissions[]
  artifactFormats[]
  dependencies[]
  errors[]
  workflowCandidates[]
  coverage
  evidenceRefs[]
~~~

### Coverage

Coverage is multidimensional.

~~~text
stateCoverage
capabilityCoverage
artifactCoverage
workflowFamilyCoverage
objectCoverage
~~~

The UI may synthesize a single headline score but the machine manifest retains each dimension.

## 6. Application Slice

~~~text
ApplicationSlice
  id
  sourceGenomeRefs[]
  selectedWorkflows[]
  objects[]
  localState
  capabilities[]
  rules[]
  calculations[]
  validations[]
  artifactImplementations[]
  worldPorts[]
  failurePolicies[]
  verificationSuite[]
~~~

The compiler must reject a slice with unresolved required dependencies unless those dependencies are represented explicitly as World Ports.

## 7. Dependency

~~~text
Dependency
  id
  kind
  description
  sourceNode
  requiredFor[]
  portability
  implementation
  worldPort?
~~~

### Kind

~~~text
bundled
user-input
snapshot
live-read
external-side-effect
~~~

### Portability semantics

~~~text
standalone
standalone-with-input
snapshot-capable
live-required
external-commit-required
unsupported
~~~

## 8. World Port

~~~text
WorldPort
  id
  capability
  inputSchema
  outputSchema
  liveMode
  snapshotMode
  permissions[]
  sideEffectScope
  idempotency
  timeoutMs
  retryPolicy
  cancellation
  auditPolicy
  privacyPolicy
~~~

A compiled package can ship no network implementation and still be valid if the workflow declares snapshot/user-input operation.

## 9. Compiled Workflow Application Manifest

Canonical conceptual shape:

~~~json
{
  "kind": "wrkflow.workflow-app",
  "schemaVersion": 1,
  "id": "...",
  "version": "...",
  "name": "...",
  "description": "...",
  "sourceReferences": [],
  "workflows": [],
  "verifiedCapabilities": [],
  "additionalVerifiedCapabilities": [],
  "inferredCapabilities": [],
  "unverifiedCapabilities": [],
  "executionProfiles": ["offline", "snapshot"],
  "requiredWorldPorts": [],
  "optionalWorldPorts": [],
  "artifactFormats": [],
  "compatibility": [],
  "verification": {
    "suiteVersion": "...",
    "testCount": 0,
    "passed": 0,
    "failed": 0
  },
  "portability": {
    "standalone": true,
    "networkRequired": false,
    "originalApplicationRequired": false
  },
  "provenance": [],
  "license": "...",
  "createdBy": "...",
  "limitations": []
}
~~~

The real runtime schema must be strict and versioned.

## 10. Capability Passport

The passport is the user-facing projection of the manifest.

It must answer:

~~~text
What can this package do?
What has actually been verified?
What more can it probably do?
What can it not do?
Does it need the original app?
Does it need the internet?
What live external systems are required?
What artifacts can it read/write?
Against which reference versions was it tested?
How much evidence supports the claims?
~~~

Example:

~~~text
Project Scheduling Workflow

Standalone: YES
Original Microsoft Project required: NO
Network required: NO

Verified:
  create tasks
  dependencies
  resources
  schedule calculation
  MSPDI export

Additional verified:
  baseline comparison

Inferred, not yet verified:
  advanced resource leveling

External dependency:
  none

Verification:
  184 differential cases
~~~

## 11. Artifact contract

~~~text
ArtifactFormat
  id
  mediaType
  version
  read()
  write()
  validate()
  normalize()
  compatibilityTargets[]
  roundTripPolicy
  unsupportedFeatures[]
~~~

All artifact operations are deterministic where feasible.

## 12. Verification contract

~~~text
VerificationCase
  id
  inputFixture
  referenceExecution?
  candidateExecution
  normalizationPolicy
  equivalencePolicy
  expectedResult
  counterexample?
~~~

### Equivalence classes

~~~text
exact
semantic
tolerant-numeric
set-equivalent
artifact-roundtrip
error-equivalent
~~~

## 13. Marketplace listing

~~~text
WorkflowListing
  id
  packageRef
  creator
  title
  description
  category[]
  price
  license
  sourceDeclarations
  capabilityPassport
  screenshots/previews?
  compatibility[]
  versionHistory[]
  verifiedMetrics
  dependencySummary
  moderationStatus
~~~

Payments and entitlement are marketplace concerns and must not be embedded inside a compiled workflow's local domain logic.

## 14. Application / package fingerprints

Fingerprints may include:

~~~text
application identity
version
frontend bundle signatures
protocol/schema signatures
artifact format signatures
native binary signatures
known DOM/accessibility traits
runtime traits
~~~

A fingerprint mismatch triggers health checks; it does not automatically invalidate a package.

## 15. Confidence

Confidence is structured, not a scalar generated from intuition.

~~~text
confidence.score
confidence.level
confidence.evidenceCount
confidence.coverage
confidence.contradictions
confidence.lastVerifiedAt
~~~

Contradictory evidence must remain visible.

## 16. Error model

All public contracts use structured error categories.

Examples:

~~~text
invalid-input
precondition-failed
capability-unresolved
dependency-required
permission-denied
reference-unavailable
verification-mismatch
artifact-incompatible
world-port-failed
package-invalid
unsupported
~~~

Do not branch on human-readable error strings.

## 17. Contract evolution

Every schema has:

- explicit schemaVersion;
- backward compatibility policy;
- migration or rejection rule;
- fixture set.

Changes to these contracts require a spec update and regression fixtures before implementation is merged.

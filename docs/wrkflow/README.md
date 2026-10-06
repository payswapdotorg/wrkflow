# Wrkflow documentation

The Wrkflow implementation source of truth is organized as follows:

1. [TL-HANDOFF.md](TL-HANDOFF.md) — the executable handoff for the Tech Lead / Orchestrator.
2. [SOURCE-OF-TRUTH.md](SOURCE-OF-TRUTH.md) — product definition and non-negotiable invariants.
3. [ARCHITECTURE.md](ARCHITECTURE.md) — system topology, boundaries, runtime profiles and planned module boundaries.
4. [DOMAIN-CONTRACTS.md](DOMAIN-CONTRACTS.md) — canonical domain objects, schemas and trust/portability contracts.
5. [IMPLEMENTATION-ROADMAP.md](IMPLEMENTATION-ROADMAP.md) — phased roadmap and three-worker concurrency plan.
6. [ACCEPTANCE.md](ACCEPTANCE.md) — product acceptance, verification and standalone gates.

The repository remains based on ZCode. Existing ZCode runtime, tooling, workflow, browser, plugin, artifact and transport systems are implementation substrates to extend where their contracts fit.

Do not treat chat history as an unstated requirement. Product behavior changes must update the relevant spec before implementation.

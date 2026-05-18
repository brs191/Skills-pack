# Gap 2 Phase B — Microservices Pattern Cards Depth Uplift

**Date:** May 18, 2026  
**Status:** Ready to start  
**Deliverable:** 21 pattern cards with pattern-specific content replacing boilerplate  

---

## Overview

Phase B completes Gap 2 (Microservices Skills Depth) by uplifting 21 microservices pattern cards from v7.3 into v8.1 with substantive, pattern-specific content.

### What was done in Phase A
- ✅ Created 12 microservices skill files (01-12) in `skills/microservices/`
- ✅ Each skill file 200-250 lines with decision frameworks, worked examples, verification questions
- ✅ Ready for Phase B: all skill files reference pattern cards and provide context for pattern usage

### What Phase B delivers
- Create `patterns/microservices/` directory in v8.1
- Uplift all 21 v7.3 pattern cards (currently 37 lines each, 92% boilerplate)
- Target 80-120 lines per card with pattern-specific content
- Eliminate word-for-word boilerplate sections
- Create structured, repeatable content across all cards

---

## Current State (v7.3 Pattern Cards)

### The Boilerplate Problem

**Example:** All 21 v7.3 pattern cards share identical sections:

```markdown
## Use When
- The problem exists in a real workflow.
- The pattern reduces coupling, improves reliability, or clarifies ownership.
- The team can operate the added complexity.

## Implementation Steps
1. Define the business capability and boundary.
2. Define API/event/data contracts.
3. Implement least-privilege identity and network access.
4. Add validation, idempotency, retry/timeout behavior where applicable.
5. Add logs, metrics, traces, and alerts.
6. Add tests and deployment automation.
7. Document an ADR for the decision.

## Common Failure Modes
- Over-engineering
- Missing ownership
- Missing observability
- Missing rollback or compensation
- Security boundary unclear

## MCP Tool Opportunities
- `recommend_microservice_patterns`
- `detect_architecture_risks`
- `generate_architecture_decision_record`
- `map_patterns_to_azure_services`
```

**Result:** Only 3–5 unique sentences per card (problem, avoid-when, Azure implementation). Everything else is copy-paste.

---

## Phase B Deliverables

### 21 Pattern Cards to Create

| # | Pattern Name | File Name | Category |
|---|---|---|---|
| 1 | API Gateway | api-gateway.md | Communication |
| 2 | Async Messaging | async-messaging.md | Communication |
| 3 | Backend for Frontend | backend-for-frontend.md | API |
| 4 | Blue-Green / Canary | blue-green-canary.md | Deployment |
| 5 | Bulkhead | bulkhead.md | Resilience |
| 6 | Cache-Aside | cache-aside.md | Data |
| 7 | Circuit Breaker | circuit-breaker.md | Resilience |
| 8 | CQRS | cqrs.md | Data |
| 9 | Database per Service | database-per-service.md | Data |
| 10 | Distributed Tracing | distributed-tracing.md | Observability |
| 11 | Event-Driven Architecture | event-driven-architecture.md | Communication |
| 12 | Event Sourcing | event-sourcing.md | Data |
| 13 | Idempotent Consumer | idempotent-consumer.md | Messaging |
| 14 | Retry-Timeout | retry-timeout.md | Resilience |
| 15 | Saga | saga.md | Data |
| 16 | Service Discovery | service-discovery.md | Communication |
| 17 | Service Mesh | service-mesh.md | Communication |
| 18 | Sidecar | sidecar.md | Deployment |
| 19 | Strangler Fig | strangler-fig.md | Deployment |
| 20 | Transactional Outbox | transactional-outbox.md | Data |
| 21 | Zero-Trust Service Access | zero-trust-service-access.md | Security |

### Target Structure Per Card (80–120 lines)

```markdown
# Pattern: [Pattern Name]

## Problem

[2–3 sentences describing the specific problem this pattern solves. Go deeper than v7.3's one sentence.]

Example: In a distributed system where multiple services need to coordinate a business transaction, 
using synchronous calls creates tight coupling and cascading failures. How do you ensure 
all steps complete, or roll back cleanly if any step fails?

## Use When

[3–5 bullets with pattern-SPECIFIC criteria, NOT generic boilerplate]

✓ Use this pattern when:
- [Specific condition 1 that applies to this pattern uniquely]
- [Specific condition 2]
- [Specific condition 3]

Example for Saga:
- A business transaction spans multiple services
- You need to coordinate compensation/rollback on failure
- Eventual consistency is acceptable

## Avoid When

[2–4 bullets with pattern-SPECIFIC constraints]

✗ Avoid when:
- [Specific case where this pattern makes things worse]
- [Specific alternative is better]

Example for Saga:
- Strong immediate consistency is required (all-or-nothing at transaction commit time)
- Compensation logic is ambiguous or expensive

## Azure Implementation

[Real implementation steps with service names and config guidance, NOT just one sentence]

### Implementation Steps

1. Identify the transaction stages (order → payment → fulfillment)
2. Choose orchestration model:
   - **Orchestration:** Use Durable Functions to coordinate steps (recommended for clear workflow)
   - **Choreography:** Use Service Bus Topics for event-driven steps
3. Implement compensation logic for each step (refund if payment fails, etc.)
4. Define timeout and retry policies for each service call
5. Set up monitoring for saga state and failures
6. Wire into the MCP server as a `generate_compensation_plan` tool

### Azure Services

| Component | Service | Configuration |
|---|---|---|
| Saga Orchestrator | Durable Functions | Activity functions per step, automatic state management |
| Messaging | Service Bus Topics | For compensation events and coordination |
| Monitoring | Application Insights | Track saga execution, failure rates, latency |

## Implementation Steps

[Pattern-SPECIFIC steps, NOT the generic 7-step boilerplate]

Example for Saga:
1. Model the saga steps as a state machine (Order Created → Payment Processing → Fulfillment)
2. Implement compensation for each step (Refund, Restore Inventory)
3. Handle partial failures and idempotent re-execution
4. Monitor execution with distributed traces
5. Test compensation flows under failure scenarios

## Trade-offs

[NEW SECTION: Explicit cost vs. benefit analysis specific to this pattern]

| Aspect | Trade-off |
|---|---|
| Consistency | Eventual consistency; final state reaches consistency after all steps |
| Complexity | High; requires compensation logic and state management |
| Testability | Must test both happy path and compensation paths |
| Operational Visibility | Need distributed tracing to debug multi-step failures |

## Common Failure Modes

[Pattern-SPECIFIC failures and detection signals, NOT generic 5-bullet list]

Example for Saga:
- **Orphaned saga:** Saga started but never completes (all steps succeed but final acknowledgment lost)
  - Detection: Long-running sagas in "waiting" state exceeding timeout
  - Prevention: Set max saga duration; archive old sagas
  
- **Cascading compensation failures:** Compensation step fails, leaving system in inconsistent state
  - Detection: Alert on compensation timeouts or errors
  - Prevention: Implement compensation idempotently; design for partial rollback

## Decision Signals

[NEW SECTION: How to recognize when to use this pattern]

Ask yourself:
- Does the business transaction require multiple services to coordinate?
- Are synchronous calls creating performance bottlenecks or tight coupling?
- Can we define clear compensation for each step?

## Azure Mapping

[Existing from v7.3, but contextualize for this specific pattern]

| Service | Role | Why |
|---|---|---|
| Durable Functions | Orchestrator | Manages saga state and step coordination |
| Service Bus | Event broker | Publishes compensation events |
| Application Insights | Observability | Traces saga execution flow |

## Go Implementation Notes

[NEW SECTION: Link to working Go code, if available]

The example MCP server at `examples/microservices-system-design-mcp-server` includes:
- `internal/services/saga/` — rule-based saga orchestration
- Tool: `generate_compensation_plan` — analyzes architecture for saga patterns

## MCP Tool Opportunities

[Existing section, but make it pattern-specific]

This pattern is detected/implemented by:
- `recommend_microservice_patterns` — suggests saga when detecting multi-service transactions
- `detect_architecture_risks` — flags saga anti-patterns (circular compensation, infinite retry loops)
- `generate_compensation_plan` — sketches compensation logic for a given saga

## Related Patterns

[NEW SECTION: Link to related patterns in this pack]

- **Transactional Outbox** — reliable event publication at the end of a saga step
- **Circuit Breaker** — resilience for individual saga steps
- **Distributed Tracing** — observability of saga execution

## References

[NEW SECTION: Links to external resources]

- Event Sourcing as an alternative to Saga: `event-sourcing.md`
- Compensation design: See `05-data-architecture.md` (Skill)
```

---

## Quality Bar

### Verification Checklist

Each pattern card passes if:

- [ ] **No boilerplate:** No sentences that could be copy-pasted to another card without changing a word
  - Grep test: Search for "The problem exists in a real workflow" — should return zero hits
  - Grep test: Search for "Define the business capability and boundary" in Implementation Steps — should return zero hits
  
- [ ] **Pattern-specific content:** Every section (Use When, Avoid When, Implementation Steps, Failure Modes) addresses THIS pattern, not generic microservices
  - Examples specific to the pattern (Circuit Breaker examples should show timeout/retry scenarios, not unrelated situations)
  
- [ ] **Practical guidance:** Azure Implementation includes actual services and configuration, not just "use Service Bus"
  - Bad: "Use Azure services for messaging"
  - Good: "Use Service Bus Topics with partitioning for ordered saga step events. Set correlation IDs for tracing"
  
- [ ] **Failure modes with signals:** Anti-patterns name the failure and how to detect it
  - Bad: "Avoid over-engineering"
  - Good: "Avoid circular compensation (A→B→A→...). Signal: Saga timeout with error 'circular dependency detected'"
  
- [ ] **Line count:** 80–120 lines (not 37)
  - Verify with `wc -l`
  
- [ ] **Cross-references:** Links to related patterns and skills
  - Each card should reference 2–3 related patterns
  - Each card should reference 1–2 relevant skills from `skills/microservices/`

### Spot-Check Examples

**Bad (boilerplate from v7.3):**
```markdown
## Use When
- The problem exists in a real workflow.
- The pattern reduces coupling, improves reliability, or clarifies ownership.
- The team can operate the added complexity.
```

**Good (pattern-specific):**
```markdown
## Use When
- Service-to-service latency is unpredictable and can cause cascading failures
- You want to fail fast instead of blocking a caller indefinitely
- You can degrade gracefully when a dependency times out (not all operations require the response)
```

---

## Integration Into v8.1

### Directory Structure

```
aaraminds-mcp-pack-v8.1/
├── skills/microservices/
│   ├── 01-design-router.md
│   ├── 02-system-design-process.md
│   ├── ...
│   └── 12-cost-and-tradeoffs.md
├── patterns/
│   └── microservices/
│       ├── api-gateway.md
│       ├── async-messaging.md
│       ├── ...
│       └── zero-trust-service-access.md
├── README.md (update to reference patterns/)
└── ROADMAP.md (mark Gap 2 as complete)
```

### Files to Update After Phase B

1. **README.md** — Add `patterns/microservices/` to pack structure section
2. **ROADMAP.md** — Mark Gap 2 (Microservices Skills Depth) as complete with verification date

---

## Roadmap Context

### Gap 2 Definition (From ROADMAP.md)

**Status:** Pending v8.1 completion

**Scope:** The 12 microservices skill files currently reuse boilerplate implementation guidance. Each pattern card has identical "Implementation Steps" language. Apply the same depth treatment we did on MCP skills.

**Files needing depth uplift:**
- 12 skill files: `skills/microservices/01-12` ✅ COMPLETE (Phase A)
- 21 pattern cards: `patterns/microservices/` ⏳ IN PROGRESS (Phase B)

**Estimated size:** 3,000–4,500 lines total

**Quality impact:** 8.5–8.7 (achieved after both phases complete)

### After Phase B Completes

**Next: Gap 3–5 (Session 4+)**

Once Phase B is verified:
1. Verify all 33 files (12 skills + 21 pattern cards) meet quality bar
2. Run final checks: line counts, boilerplate grep, cross-references
3. Update README.md and ROADMAP.md
4. Proceed to Gap 3: Microservices skills optimization + pattern card depth (session 4)

---

## Working Session Setup (Other System)

### Prerequisites
- SkillsPack.zip extracted to your working directory
- Text editor or IDE (VS Code recommended)
- Git (to commit and push when done)

### Suggested Workflow

**Day 1: Foundation**
1. Extract SkillsPack.zip
2. Read skill files 06 (Resilience), 07 (Async Messaging) for context
3. Create 3 pattern cards (Circuit Breaker, Retry-Timeout, Saga) — core resilience patterns
4. Verify structure and depth against quality bar
5. Commit: "Phase B session 1: Foundation patterns (3 cards)"

**Day 2: Communication Patterns**
1. Create 4 pattern cards (Async Messaging, Event-Driven, API Gateway, Service Discovery)
2. Cross-reference with skill 07 and 08
3. Commit: "Phase B session 2: Communication patterns (4 cards)"

**Day 3: Data Patterns**
1. Create 4 pattern cards (CQRS, Event Sourcing, Outbox, Database-per-Service)
2. Cross-reference with skill 05
3. Commit: "Phase B session 3: Data patterns (4 cards)"

**Day 4: Remaining Patterns**
1. Create remaining 10 pattern cards (deployment, security, observability)
2. Final verification: line counts, boilerplate check, cross-references
3. Commit: "Phase B session 4: Final patterns (10 cards)"

**Final: Integration**
1. Update README.md and ROADMAP.md
2. Verify directory structure
3. Final commit: "Phase B complete: All 21 pattern cards with depth uplift"
4. Push to https://github.com/brs191/Skills-pack

---

## Reference: Phase A Deliverables

All 12 skill files are in the SkillsPack.zip under `aaraminds-mcp-pack-v8.1/skills/microservices/`:

1. **01-design-router.md** — Routing logic and reasoning sequence
2. **02-system-design-process.md** — 11-stage design methodology
3. **03-domain-decomposition.md** — DDD, bounded contexts, decision framework
4. **04-service-boundaries.md** — Data ownership, consistency rules, verification
5. **05-data-architecture.md** — CQRS, saga, outbox, event sourcing patterns
6. **06-resilience-patterns.md** — Timeout, retry, circuit breaker, bulkhead, degradation
7. **07-async-messaging.md** — Pub-sub, choreography, orchestration, ordering
8. **08-api-design.md** — REST vs gRPC, versioning, error handling, gateway
9. **09-azure-mapping.md** — Container Apps, SQL, Redis, Service Bus decisions
10. **10-observability-design.md** — Tracing, metrics, structured logs, SLOs
11. **11-security-design.md** — Auth, authz, encryption, audit, compliance
12. **12-cost-and-tradeoffs.md** — Total cost, monolith vs microservices, optimization

---

## Contact / Questions

If questions arise during Phase B:
- Refer to the corresponding skill file (e.g., for Saga pattern, read `05-data-architecture.md`)
- Check the worked examples in skill files for context
- Cross-reference related patterns in v7.3 `patterns/microservices/` for the base structure

---

**Status:** Ready to proceed to another system  
**Artifact:** SkillsPack.zip containing Phase A (12 skills complete) + v7.3 reference  
**Next milestone:** All 21 pattern cards with pattern-specific depth (80–120 lines each)

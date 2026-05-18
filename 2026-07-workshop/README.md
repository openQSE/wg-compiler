# Compiler Infrastructure Workstream — OpenQSE Day, July 24, 2026

*Draft for co-lead review — Josh Moles*

Proposing: Compiler ↔ QDMI contract spec + reference adapter producing correct lowered output for one compiler/backend pair (performance parity out of scope) as our 6-month deliverable. Ideally a second compiler stack cross-validates. Workshop paper co-developed alongside.

*Reference adapter:* code within an existing compiler stack that consumes QDMI target descriptions and uses them to drive lowering.

Input wanted: Right primary deliverable? Challenges and breakout structure? 6-month timeline given QDMI dependency? Which compiler stack each of us can implement against?

## Workstream Overview

- Workstream Name: Compiler Infrastructure
- Lead(s): Josh Moles (IonQ), Narasinga Rao Miniskar (ORNL), HPE (Andy, Daniel, Michael F)
- Contributors / Organizations: IonQ, HPE, ORNL; broader TBD from registration
- Relevant Stack Layer(s): Compiler / IR — Tool/Compiler Pipeline (Shehata §8.3)

## Scope & Focus

What is this workstream responsible for?

- In-scope:
  - Compiler ↔ QDMI contract definition
  - Reference adapter in at least one compiler stack
  - Lowering pipeline integration with the contract
  - Cross-vendor portability validation
- Out-of-scope:
  - Device interface itself (QDMI workstream)
  - Resource management / submission semantics (QRMI is the downstream interface; not v0.1)
  - Runtime/orchestration (Software Architecture workstream)
  - SDK design
  - Performance optimization through the contract
  - Full IR standardization

Key problems this workstream aims to address:

- Compiler ↔ QDMI contract undefined
- No reference implementation proving a vendor-neutral contract is viable
- No portable pass interface (deferred)

## Alignment with OpenQSE Objective

How does this workstream contribute to the overall OpenQSE mission?

- Contribution to:
  - Interoperability: Working contract + adapter shows compilers can consume target descriptions from a vendor-neutral source
  - Vendor-neutral interfaces: Contract is the interface; cross-vendor implementations validate it
  - HPC–QC integration: The compiler ↔ QDMI seam is where target-aware compilation meets vendor-neutral device descriptions

## Session Structure (Day-of Plan)

~4.5 hrs, 10–15 attendees, in-person:

| Time | Activity |
| --- | --- |
| 0:00–0:15 | Intro talk |
| 0:15–0:45 | Challenge 1 (full group) |
| 0:45–2:15 | Challenge 2 breakout |
| 2:15–2:45 | Working lunch |
| 2:45–4:15 | Challenge 3 breakout |
| 4:15–4:45 | Synthesis + milestone lock |
| 4:45–end | Buffer |

### A. Intro Talk (10–15 min)

- Presenter: Josh Moles
- Goal:
  - Set context — compiler layer in openQSE architecture
  - Introduce problem space — proposed deliverable (contract spec + reference adapter + workshop paper, co-developed)
- Key points to cover:
  - Compiler layer in openQSE architecture diagram
  - Proposed deliverable and out-of-scope items
  - Walk through challenges

### B. Pre-defined Discussion Topics / Challenges

(Prepared in advance and shared before the event)

**Challenge 1: Contract scope and implementer commitments (full-group, 30 min)**

- Description: Lock contract scope, identify compiler stacks for reference adapters, align on validation plan
- Why it matters: Determines whether we ship a real artifact in 6 months
- Key questions:
  - What's in v0.1? (Gate set, topology, timing, mid-circuit measurement, conditional control)
  - Which compiler stacks commit as primary and cross-validation?
  - Target venue family: SC, QCE, QSW workshops — chosen based on CFP timing

**Challenge 2: Contract definition (breakout, 90 min)**

- Description: Work through the technical content of the contract
- Why it matters: This is the spec we're shipping
- Key questions:
  - Minimum target description for correct lowering?
  - How are vendor-specific capabilities exposed without breaking portability?
  - Querying QDMI vs. embedding hardware knowledge?
  - What does the contract explicitly *not* carry?

**Challenge 3: IR landscape and follow-on directions (breakout, 90 min)**

- Description: Characterize IR landscape and pass infrastructure; identify follow-on work
- Why it matters: Frames the next 6–12 months after v0.1 ships
- Key questions:
  - Where is IR convergence happening (or not)?
  - What pass infrastructure work becomes possible after v0.1?
  - Workshop paper sufficient, or separate spec document needed?

### C. Breakout / Discussion Format

- Format: Hybrid — full-group opening + 2 sequential breakouts
- Number of groups: 1 group, all attendees together for each segment
- Topics per group: Challenge 1 (full group), Challenge 2 (breakout 1), Challenge 3 (breakout 2)

### D. Synthesis Plan

- How results will be consolidated:
  - Scribe summary using pre-structured template; live capture in repo

## Expected Outputs (End of Day)

Each workstream must produce:

### A. Key Gaps Identified (locked at event)

- Gap 1: No defined compiler ↔ QDMI contract
- Gap 2: No reference implementation proving vendor-neutral contract is viable
- Gap 3: No cross-vendor compiler portability path

### B. Proposed Interfaces / Specifications (if applicable)

- Interface / API candidate: Compiler ↔ QDMI target description contract (v0.1)
- Scope: Correct lowering across vendors; performance parity out of scope
- Dependencies: QDMI v0.1 stable by ~September 2026

### C. Milestones (Next 6 Months)

**Milestone 1:**

- Description: Contract scope locked; compiler stacks chosen; paper outline drafted
- Timeline: Late August 2026
- Owner(s): Leads + QDMI co-leads

**Milestone 2:**

- Description: Draft contract spec; adapter implementation underway; paper intro/related work/contract design drafted
- Timeline: End of October 2026
- Owner(s): Implementer + spec authors

**Milestone 3:**

- Description: Working reference adapter; spec v0.1; paper draft substantially complete
- Timeline: End of December 2026
- Owner(s): Implementer + leads

**Milestone 4:**

- Description: Cross-validation in second stack; paper revisions; submission-ready
- Timeline: January–February 2027
- Owner(s): Second implementer + leads

**Milestone 5:**

- Description: Submit to nearest-fit workshop with open CFP
- Timeline: When M4 complete
- Owner(s): Leads

### D. Deliverables

(Concrete outputs, not just discussion)

Committed for the 6-month window:

- Compiler ↔ QDMI contract spec (v0.1)
- Reference adapter in at least one compiler stack
- Cross-validation: second compiler stack implementing the same contract
- Workshop paper documenting spec + implementations

Deferred to follow-on effort:

- Performance-optimized adapters
- IR conformance profile
- Pass interface specification
- SC main track or ACM TQC submission (~18–24 months) building on workshop paper

## Implementation & Validation Considerations

- Existing efforts to leverage: QDMI v0.1; QIR Alliance; OpenQASM 3; MLIR dialects (Catalyst, CUDA-Q NVQ++, MQT Core); IonQ, ORNL, HPE compiler stacks; Genesis
- Potential contributors (vendors / labs): IonQ, ORNL, HPE; cross-validation candidates include NVIDIA (CUDA-Q), Xanadu (Catalyst), TUM/MQV (MQT Core), Quantinuum (TKET) — commitments solicited at the event
- Validation approach:
  - Reference adapter producing correct lowered output
  - Cross-vendor implementation in a second compiler stack
  - Integration demo with QDMI's reference implementation if available

## Dependencies & Interfaces with Other Workstreams

- Depends on: QDMI v0.1 stable by ~September 2026 (hard dependency)
- Provides to: QDMI (compiler-side validation); Software Architecture (working compiler integration)
- Required coordination: Pre-event sync with QDMI leads (Park, Schulz, Naughton) by June 14; ongoing through M1–M3

## Pre-Event Preparation Checklist

- [ ] Co-lead alignment on direction
- [ ] QDMI pre-meeting completed
- [ ] Intro slides drafted
- [ ] Pre-read shared (Shehata paper + challenge briefs)
- [ ] Participants identified
- [ ] Scribe assigned

## Notes / Open Questions

- Authorship norms for spec + paper — sorted early M1
- QDMI dependency: if v0.1 slips past September, timeline compresses
- SC26 panel: spec + implementation could anchor narrative
- Reference adapter scope (correctness, not performance parity) stated explicitly in spec to manage vendor expectations

## "1-Year Vision" Prompt

"If this workstream is successful, what exists 1 year from now that does not exist today?"

- A published workshop paper documenting the contract spec (v0.1) and reference adapter implementations across two compiler stacks. Workshop paper establishes the artifact; main track or journal paper at ~18–24 months, contingent on adoption and deployment results, is the natural follow-on.

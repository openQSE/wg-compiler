# Compiler and Optimization Pass Infrastructure: Discussion Summary

## Table of Contents

* [Executive Summary](#executive-summary)
* [Background](#background)
* [Initial Concept](#initial-concept)
* [Two Conceptual Layers](#two-conceptual-layers)

  * [Layer 1: Pass Management Infrastructure](#layer-1-pass-management-infrastructure)
  * [Layer 2: IR and Artifact Contracts](#layer-2-ir-and-artifact-contracts)
* [A Key Realization](#a-key-realization)
* [Qiskit vs MLIR: A False Dichotomy](#qiskit-vs-mlir-a-false-dichotomy)

  * [What Qiskit Provides](#what-qiskit-provides)
  * [What MLIR Provides](#what-mlir-provides)
* [The Flexibility Problem](#the-flexibility-problem)
* [The Explosion Problem](#the-explosion-problem)
* [A Possible Compromise](#a-possible-compromise)
* [Open Question: One IR or Many?](#open-question-one-ir-or-many)
* [Current Working Hypothesis](#current-working-hypothesis)
* [Proposed openQSE Action Items](#proposed-openqse-action-items)

---

# Executive Summary

<details open>
<summary><strong>Expand</strong></summary>

This discussion began with a question about whether openQSE should investigate a common infrastructure for compiler passes and optimization passes, including emerging algorithm-specific optimizations such as VQE optimizations, QAOA optimizations, error mitigation, resource estimation, FTQC transformations, quantum networking, and runtime orchestration.

The initial idea was to define a common transformation ecosystem in which applications can discover available passes, compose pipelines, and execute transformations independently of the underlying compiler implementation.

Through the discussion, a key distinction emerged:

> Standardizing a pass infrastructure and standardizing an IR are different problems.

A pass infrastructure defines how transformations are discovered, described, composed, validated, and executed. An IR defines how information is represented and exchanged between transformations. While these concepts are related, they do not necessarily need to be standardized together.

The discussion also highlighted that current conversations within the quantum community often focus on IR selection, particularly MLIR-based approaches. However, openQSE should be careful not to prematurely constrain the design space. Existing systems such as the Qiskit transpiler provide mature and widely deployed examples of pass-management and transformation frameworks that should be evaluated alongside MLIR and other compiler infrastructures.

A recurring theme was the tension between flexibility and interoperability.

On one hand, the field is still evolving rapidly, and different domains may require different representations and abstractions. Examples include:

* Chemistry optimizations
* FTQC transformations
* Error mitigation
* Quantum networking
* Runtime orchestration
* Hybrid HPC/QC workflows

On the other hand, allowing every pass to define arbitrary input and output types risks creating an ecosystem of incompatible components.

The discussion therefore converged on the idea that openQSE may ultimately need to standardize some combination of:

* Pass contracts
* Artifact categories
* Feature profiles
* Exchange representations
* Validation mechanisms
* Provenance mechanisms

while still allowing multiple implementation strategies and potentially multiple IRs underneath.

Rather than immediately asking:

> Which IR should openQSE adopt?

the more fundamental question may be:

> What should openQSE standardize to maximize interoperability while preserving innovation?

The current working hypothesis is that openQSE should first perform a structured survey of existing compiler ecosystems, evaluate pass-management and IR technologies independently, define objective evaluation criteria, and determine whether the highest-value standardization opportunity lies in a common IR, a common transformation ecosystem, or a combination of both.

</details>

---

# Background

<details>
<summary><strong>Expand</strong></summary>

One of the topics that has emerged during discussions around openQSE is whether there is value in defining a common infrastructure for compiler passes and optimization passes.

Traditionally, quantum compilation has focused on transforming circuits into representations that can execute on specific hardware. However, there is increasing interest in additional classes of transformations, including:

* Algorithm-specific optimizations (VQE, QAOA, chemistry, etc.)
* Error mitigation
* Resource estimation
* FTQC transformations
* Runtime orchestration
* Quantum networking
* Hardware-specific optimizations

This raises the question:

> Should openQSE define a common infrastructure that allows independently developed transformations to be discovered, composed, and executed as part of a common tool pipeline?

</details>

---

# Initial Concept

<details>
<summary><strong>Expand</strong></summary>

The initial idea is to build a common transformation infrastructure that can be configured by an application.

An application could:

* Query available passes
* Discover capabilities
* Compose a pipeline
* Execute that pipeline on one or more circuits

For example:

```text
Application
    ↓
VQE Optimization Pass
    ↓
Error Mitigation Pass
    ↓
Hardware Mapping Pass
    ↓
Scheduling Pass
    ↓
Pulse Generation Pass
    ↓
QPU
```

In this model, the application controls the pipeline while the infrastructure provides the mechanisms for pass discovery, composition, validation, and execution.

</details>

---

# Two Conceptual Layers

<details>
<summary><strong>Expand</strong></summary>

A recurring theme in the discussion was that there are actually two separate problems.

## Layer 1: Pass Management Infrastructure

This layer answers the question:

> How are transformations discovered, composed, and executed?

Potential capabilities include:

```text
register_pass()
deregister_pass()
query_passes()
get_pass_contract()
compose_pipeline()
validate_pipeline()
run_pass()
```

Responsibilities include:

* Pass registration
* Capability discovery
* Pipeline composition
* Dependency management
* Validation
* Execution
* Provenance tracking

This layer is largely independent of the actual IR being used.

---

## Layer 2: IR and Artifact Contracts

This layer answers the question:

> What does a pass consume and what does it produce?

Potential contract elements include:

* IR identity
* IR version
* Artifact type
* Dialects used
* Feature profiles
* Semantic guarantees
* Provenance information
* Validation rules

Example:

```text
Input:
    LogicalCircuit
    OpenQASM3
    symbolic-parameters

Output:
    MappedCircuit
    MLIR-Quantum
    physical-qubits
```

</details>

---

# A Key Realization

<details>
<summary><strong>Expand</strong></summary>

A major realization during the discussion was that:

> Standardizing a pass infrastructure and standardizing an IR are different problems.

These are related, but they do not necessarily need to be solved simultaneously.

A common pass infrastructure can provide value even if multiple IRs exist underneath it.

For example:

```text
VQE Optimization
    CircuitIR -> CircuitIR

Mapping Pass
    CircuitIR -> MappedCircuitIR

Scheduling Pass
    MappedCircuitIR -> ScheduledCircuitIR

Pulse Generation
    ScheduledCircuitIR -> PulseIR

Resource Estimation
    LogicalIR -> ResourceEstimate
```

The pass infrastructure cares about whether passes can be composed.

The passes themselves determine how transformations occur.

</details>

---

# Qiskit vs MLIR: A False Dichotomy

<details>
<summary><strong>Expand</strong></summary>

Initially, the discussion was framed as:

```text
Qiskit vs MLIR
```

However, after further analysis, this framing appears overly simplistic.

A better framing may be:

```text
Qiskit Pass Framework
            +
MLIR IR Framework
```

These solve different problems.

## What Qiskit Provides

Qiskit provides:

* Pass management
* Pass composition
* Analysis passes
* Transformation passes
* Pipeline execution

The Qiskit transpiler is a mature example of a pass-based transformation framework.

Importantly, the concepts embodied in the Qiskit pass manager are not inherently IBM-specific.

One could imagine:

* Extracting the pass manager into a standalone project
* Reimplementing similar concepts independently
* Adopting its architectural ideas without adopting the rest of Qiskit

Therefore, Qiskit's pass infrastructure should be considered as part of any landscape survey.

## What MLIR Provides

MLIR provides:

* Multi-level IR infrastructure
* Dialects
* Progressive lowering
* Conversion frameworks
* Type systems
* Validation mechanisms

MLIR primarily addresses representation and transformation between representations.

It is not directly a pass marketplace or transformation ecosystem.

</details>

---

# The Flexibility Problem

<details>
<summary><strong>Expand</strong></summary>

A major concern raised during the discussion was balancing flexibility and interoperability.

At first glance, allowing passes to declare arbitrary input and output types appears attractive:

```text
Pass A:
    IR_A -> IR_B

Pass B:
    IR_C -> IR_D
```

However, this quickly leads to a problem.

If every pass can define arbitrary:

* Inputs
* Outputs
* Encodings
* Metadata schemas
* Feature definitions

then the ecosystem becomes fragmented.

The infrastructure becomes a registry of incompatible components.

</details>

---

# The Explosion Problem

<details>
<summary><strong>Expand</strong></summary>

This concern can be summarized as:

> How do we avoid an explosion of incompatible pass contracts?

Even if passes declare their contracts, unconstrained flexibility still produces an unmanageable ecosystem.

The same concern applies to:

* Artifact classes
* Encodings
* Feature profiles
* Metadata definitions

Without bounds, every vendor could invent their own representation.

The discussion concluded that simply allowing arbitrary pass contracts is insufficient. Some degree of standardization or governance is necessary.

</details>

---

# A Possible Compromise

<details>
<summary><strong>Expand</strong></summary>

One possible middle ground is bounded flexibility.

Instead of requiring a single universal IR, openQSE could define:

## Standard Artifact Classes

Examples:

```text
LogicalCircuit
MappedCircuit
ScheduledCircuit
PulseProgram
ResourceEstimate
RuntimeWorkflow
```

## Standard Encodings

Examples:

```text
OpenQASM3
QIR
MLIR Quantum Dialects
Qiskit DAG Adapter
```

## Standard Feature Profiles

Examples:

```text
symbolic-parameters
physical-qubits
timing
calibration-data
dynamic-control
error-correction-metadata
```

Passes would declare support for combinations of these standardized concepts.

This allows interoperability while avoiding unrestricted growth of representations.

However, it should be noted that this approach still requires governance. Without limits on artifact classes, encodings, and feature profiles, the ecosystem can still experience combinatorial growth and fragmentation.

</details>

---

# Open Question: One IR or Many?

<details>
<summary><strong>Expand</strong></summary>

A recurring question throughout the discussion was:

> Should openQSE define a common IR?

or

> Should openQSE define a common transformation ecosystem that can support multiple IRs?

Arguments for a common IR:

* Simpler interoperability
* Easier pass composition
* Reduced conversion overhead
* Easier conformance testing

Arguments against prematurely selecting a common IR:

* The field is still evolving rapidly
* Different domains may require different abstractions
* FTQC requirements remain uncertain
* Runtime orchestration requirements are still emerging
* Quantum networking may introduce new abstractions

At this stage, it is not clear that a single representation can satisfy all future use cases.

</details>

---

# Current Working Hypothesis

<details open>
<summary><strong>Expand</strong></summary>

A potential direction is:

* Standardize pass discovery and composition
* Standardize pass contracts
* Standardize artifact categories
* Standardize feature profiles
* Define preferred exchange formats
* Allow multiple implementation strategies underneath

This would encourage interoperability while preserving flexibility and innovation.

The fundamental question remains:

> What should openQSE standardize to maximize interoperability while preserving innovation?

rather than:

> Which IR should openQSE adopt?

</details>

---

# Proposed openQSE Action Items

<details open>
<summary><strong>Expand</strong></summary>

## 1. Survey Existing Compiler Ecosystems

Perform a structured survey of existing approaches, including:

* MLIR
* Qiskit
* QIR ecosystems
* OpenQASM tooling
* LLVM-based approaches
* Vendor-specific compiler infrastructures

The goal should be understanding architectural tradeoffs rather than selecting a winner.

---

## 2. Compare Pass Frameworks Independently of IRs

Evaluate:

* Pass composition
* Dependency management
* Capability discovery
* Plugin models
* Pipeline validation
* Provenance tracking

---

## 3. Compare IR Frameworks Independently of Pass Frameworks

Evaluate:

* Expressiveness
* Extensibility
* Multi-level representations
* Dialect support
* Lowering mechanisms
* Validation mechanisms
* Long-term maintainability

---

## 4. Identify Evaluation Criteria

Potential criteria include:

* Extensibility
* Vendor neutrality
* Ease of adoption
* Ecosystem maturity
* Ability to support FTQC
* Ability to support hybrid HPC/QC workflows
* Ability to support future runtime models
* Interoperability potential

---

## 5. Identify Common Artifact Categories

Investigate whether a small set of common artifact classes can be standardized.

Examples:

* LogicalCircuit
* MappedCircuit
* ScheduledCircuit
* PulseProgram
* ResourceEstimate
* RuntimeWorkflow

---

## 6. Identify Common Feature Profiles

Investigate whether common feature profiles can be standardized.

Examples:

* Symbolic parameters
* Dynamic circuits
* Timing information
* Calibration information
* Error correction metadata

---

## 7. Investigate the Feasibility of a Common Pass Ecosystem

Determine whether openQSE should focus on:

* A common IR
* A common pass ecosystem
* Both

and identify where interoperability provides the greatest value.

---

## 8. Produce an Architecture Position Paper

Summarize findings and recommendations.

The paper should clearly separate:

* Pass infrastructure recommendations
* IR recommendations
* Standardization opportunities
* Areas where experimentation should remain encouraged

The goal should be to provide a technical basis for future openQSE decisions rather than prematurely converging on a specific implementation.

</details>


# June Discussion Summary

## Proposed Plan

(from Amir):
* Survey paper of compilers would expose the different IRs used during compilation (distinguishing these with interchange formats). 
* From there we can select the best suited IR based on some criteria we still need to come up with. This could be part of the job of this working group
* Once we have that IR, then passes which take as input this IR and output that IR
* Additionally, having infrastructure which allows new passes to be registered, unregistered, into the tool pipeline would be useful, and relatively easy to accomplish


## Key Open Questions
- Should the working group define: (a) a standard compiler IR, (b) an interchange format, (c) a pass-request interface, (d) standard transformation definitions, or some subset?
- Should the effort focus only on compiler passes, or also include algorithm-specific transformations?
- Do users need to specify specific compilation passes as part of their applications? How much compilation pipeline configurability should be exposed to users vs kept as curated presets?
- Should the scope include FTQC/QEC?

(Homework from other groups)
- resource management / interfaces, wants to know the type of data the compiler will need from the device

## Draft IR Selection Criteria

Note, whether the group should select a common IR as the compiler middle layer is an open question. (This would be MLIR dialects if using MLIR)
Assumption: The effort will not select a single interchange format and rather work with whatever is needed for the SDKs and devices (OpenQASM 2, OpenQASM 3, QIR, Jeff)

If selecting an IR, here are some criterion:
- FTQC extensibility
- reusable circuit/function abstraction
- realistic classical control flow
- qubit allocation model
- extensibility
- existing implementation maturity
- translation support
- simulation/noise support.

Note that in FTQC efforts it's likely that multiple IRs will be needed.

## Adapters and Translation Model
- Is one adapter per SDK the intended baseline, and who should own adapter development (OpenQSE, vendors, or hybrid via common interchange export)?
- Can adapter count/maintenance be reduced further without shifting complexity elsewhere?
- Is round-trip flow through SDK representations (SDK -> IR -> SDK) a core requirement or only a special-case need?

Resources about MLIR and OpenQASM (related to Qiskit -> MLIR -> Qiskit
patterns)
 * https://github.com/openqasm/qe-compiler has an OpenQASM 3 MLIR dialect
 * https://github.com/Munich-Quantum-Software-Stack/MQSS-Quantum-Compilation-Suite/blob/develop/lib/Passes/CodeGen/QASM3ToQuake.cpp
   MQSS has an OpenQASM parser into Quake

## Passes, Pipeline Design, and Testing
- What pass categories are in scope (translation, optimization, FTQC-specific lowering, algorithm-specific)?
- Should OpenQSE standardize pass orchestration APIs, given testing burden from arbitrary pass combinations?
- Can a dual model work: simple default pipelines for most users plus advanced reconfiguration for experts?

## Optimization Framework Questions
- Should OpenQSE evaluate integration paths for techniques like ZX-calculus or Pauli-product-rotation rewrites?
- How should optimization approaches that work on circuit-only representations handle classical control flow?
- If ML/LLM-based optimization methods are considered, where would they fit in the tool pipeline?

 See also https://www.mdpi.com/2624-960X/7/1/2

## Hybrid Classical-Quantum Circuits
- What minimum runtime/control-system assumptions should be required for classical-quantum feedback?
- Should the framework emulate measurement+conditional behavior where hardware support is limited, or require baseline capabilities?
- How much classical-side freedom is safe without creating unmanageable timing/coordination risks?

## Process and Near-Term Work
- Which survey outputs are expected by end-of-July: landscape only, or also preliminary recommendations?
- Should the group schedule an educational compiler-design session to align terminology and baseline understanding?
- What minimal milestone set should be committed now (survey, criteria, prototype adapters, sample passes)?

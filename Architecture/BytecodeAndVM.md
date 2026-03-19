# Bytecode and Virtual Machine Architecture

The core of Vext execution occurs natively within `VextVM.cs`. This document provides compiler engineers deep knowledge concerning how ASTs translate down to pure linear processing natively natively.

---

## 1. Instruction Set Architecture (Bytecode)

Vext is not interpreted instruction by instruction via abstract nodes. `SemanticPass.cs` aggressively compiles logic into a direct flat array list sequentially (`List<Instruction>`). Each instruction contains:
- **`OpCode`** (E.g. `VextVMBytecode.ADD`, `STORE_VAR`)
- **`Arg`** (A generic object tracking jump integer targets, variable indices, or numeric literals cleanly)

### Critical Opcodes Overview

| Opcode | Objective Function |
| :--- | :--- |
| `LOAD_CONST` | Pushes a numeric or string literal from `ArgVal` to the `stack`. |
| `LOAD_VAR`   | Takes the `ArgInt` slot index, reads from `variables[]`, and pushes to the `stack`. |
| `STORE_VAR`  | Pops the top `stack` limit and assigns it linearly to `variables[ArgInt]`. |
| `ADD` / `SUB` | Math endpoints. Pops 2 values sequentially, calculates, and pushes the result. If string natively, concat triggers globally. |
| `JMP`        | Adjusts `ip` (Instruction Pointer) directly to `ArgInt` limits globally. |
| `JMP_IF_FALSE`| Pops `bool` stack. If false, executes absolute `JMP` limit mapping structural boundaries (used heavily in `while` and `if` exits). |
| `CALL`       | Executes native dictionary Hook mappings spanning `Sp` limits cleanly seamlessly seamlessly. |

---

## 2. Memory Formats (The Heapless Stack)

`VextVM.cs` actively attempts to prevent allocation cycles during loops cleanly natively to reduce C# GC spikes natively mapping. 

- **Stack Execution Length Limit**: `private VextValue[] stack = new VextValue[256];`
- **Variable Storage Array Limit**: `private VextValue[] variables = new VextValue[64];`

Memory sizes expand natively `(Length * 2)` but default initialization avoids overhead entirely linearly. The Pointer mappings (`ref int sp` tracking lengths) avoid list overhead completely completely completely.

---

## 3. Type Bridging the `ADD` Penalty

Because `VextValue` mimics a dynamic C Union (housing integer/float variables across specific memory mappings globally seamlessly), calculating addition dynamically generates penalty overhead determining the proper C# hook globally natively:

1. **Check For Strings first:** If either structure limits map `VextType.String`, explicitly fall outward to `.ToString()` sequence completely bypassing Math natively natively.
2. **Determine Exact Primitive Formats:** If *both* are integers completely, the execution evaluates native `long lInt + long rInt` limits flawlessly.
3. **Double Cast:** If integers and floats mix entirely across execution seamlessly, `rNum = right.ToDouble()` expands integers natively before performing explicit standard Math `.Pow` or calculation natively natively natively. Avoid mixed typing when maximum structural performance matters!

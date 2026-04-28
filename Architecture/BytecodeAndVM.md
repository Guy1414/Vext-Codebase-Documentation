# Bytecode and Virtual Machine Architecture

The core of Vext execution occurs natively within `VextVM.cs`. This document provides compiler engineers deep knowledge concerning how ASTs translate down to pure linear processing natively natively.

---

## 1. Instruction Set Architecture (Bytecode)

Vext is not interpreted instruction by instruction via abstract nodes. `SemanticPass.cs` aggressively compiles logic into a direct flat array list sequentially (`List<Instruction>`). Each instruction contains:
- **`Op`** (E.g. `VextVMBytecode.ADD_INT`, `STORE_VAR`)
- **`Arg`** (A generic object tracking jump targets, variable indices, or literals)

### Critical Opcodes Overview

| Opcode | Objective Function |
| :--- | :--- |
| `LOAD_CONST` | Pushes a numeric or string literal from `ArgVal` to the `stack`. |
| `LOAD_VAR`   | Takes the `ArgInt` slot index, reads from `variables[]`, and pushes to the `stack`. |
| `STORE_VAR`  | Pops the top `stack` limit and assigns it linearly to `variables[ArgInt]`. |
| `ADD_INT` / `SUB` | Math endpoints. Pops 2 values, calculates, and pushes result. |
| `CONCAT_STRING`| Pops 2 values and performs string concatenation. |
| `INC_VAR` / `DEC_VAR` | Increments or decrements a variable slot directly by 1. |
| `JMP`        | Adjusts `ip` (Instruction Pointer) directly to `ArgInt` limits globally. |
| `JMP_IF_FALSE`| Pops `bool` stack. If false, executes absolute `JMP` limit mapping structural boundaries (used heavily in `while` and `if` exits). |
| `CAST_INT` / `CAST_FLOAT` / `CAST_BOOL` | Converts the top stack value to the specified type. |
| `CALL`       | Executes native dictionary Hook mappings spanning `Sp` limits cleanly seamlessly seamlessly. |

---

## 2. Memory Formats (The Heapless Stack)

`VextVM.cs` actively attempts to prevent allocation cycles during loops cleanly natively to reduce C# GC spikes natively mapping. 

- **Stack Length**: `private VextValue[] stack = new VextValue[256];`
- **Variable Storage**: `private VextValue[] variables = new VextValue[64];` (Expands dynamically)

Memory sizes expand natively `(Length * 2)` but default initialization avoids overhead entirely linearly. The Pointer mappings (`ref int sp` tracking lengths) avoid list overhead completely completely completely.

---

## 3. Type Bridging the `ADD` Penalty

Because `VextValue` mimics a dynamic C Union (housing integer/float variables across specific memory mappings globally seamlessly), calculating addition dynamically generates penalty overhead determining the proper C# hook globally natively:

1. **Check For Strings first:** If either structure limits map `VextType.String`, explicitly fall outward to `.ToString()` sequence completely bypassing Math natively natively.
2. **Determine Exact Primitive Formats:** If *both* are integers completely, the execution evaluates native `long lInt + long rInt` limits flawlessly.
3. **Double Cast:** If integers and floats mix entirely across execution seamlessly, `rNum = right.ToDouble()` expands integers natively before performing explicit standard Math `.Pow` or calculation natively natively natively. Avoid mixed typing when maximum structural performance matters!

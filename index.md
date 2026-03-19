# Vext Internal Codebase Documentation

Welcome to the internal engineering documentation for the **Vext Compiler and Virtual Machine** architecture. 

If you are reading this, you are a compiler engineer, standard library maintainer, or core contributor aiming to expand the capabilities of the Vext execution engine. This documentation suite covers the complete lifecycle of a Vext program, from raw character lexing to bytecode execution cycles within the JVM loop.

---

## 🏗️ Architecture Overview

The Vext codebase enforces a strict separation of concerns, fundamentally split between AOT compilation and structural execution. 

- **Vext (The Compiler):** Operates on the source text string. It owns `Lexer.cs`, `Parser.cs`, and `SemanticPass.cs`. It is completely ignorant of how bytecode is fundamentally executed array-wise.
- **Runtime (The Virtual Machine):** The `VextVM.cs` operates exclusively on bytecode. It contains fixed-size arrays for stacks, mathematical instruction mappings, and native C# method hook registries.
- **Vext.Shared:** The bridging domain. It holds `VextVMBytecode`, AST `Nodes`, and the ultra-critical `VextValue` struct mapping runtime memory.
- **Tooling:** The `Vext.LSP` handles JSON-RPC standard telemetry across standard standard I/O pipes. 

---

## 📚 Engineering Resources

To maintain consistency and extreme performance across the Vext suite, please review the critical system mappings:

### 1. Developer Guidelines
- **[Contributing & Style Guide](Contributing/StyleGuide.md):** The mandatory C# conventions spanning everything from `Allman` brace placement to implicit scoping techniques.

### 2. Engine Architecture
- **[Compiler Pipeline](Architecture/CompilerPipeline.md):** A deep dive into Token generation, Recursive Descent Parsing boundaries, and Semantic Scope extraction (`Stack<BitArray>`).
- **[Bytecode and the VM](Architecture/BytecodeAndVM.md):** The core of Vext execution. Understand the 256-limit bytecode stack, instruction limits, `JMP_IF_FALSE` offsets, and operational limits.
- **[Shared Data Structures](Architecture/SharedDataStructures.md):** Learn why `VextValue` uses `StructLayout(LayoutKind.Explicit)` to simulate a C Union, bypassing C# runtime garbage collection pauses natively.

### 3. Peripheral Tooling
- **[Language Server Protocol (LSP)](Tooling/LanguageServer.md):** Understand the background `Vext.LSP.exe` daemon architecture, VS Code Node.js extension clients, and text syncing mechanics.

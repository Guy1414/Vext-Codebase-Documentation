# Vext Internal Codebase Documentation

Welcome to the internal engineering documentation for the **Vext Compiler and Virtual Machine** architecture.

This documentation suite is designed for compiler engineers, standard library maintainers, and core contributors. It covers the complete lifecycle of a Vext program, from raw character lexing to bytecode execution cycles within the virtual machine.

---

## 🏗️ Architecture Overview

The Vext codebase enforces a strict separation of concerns, fundamentally split between AOT compilation and structural execution.

- **Vext (The Compiler):** Operates on source text. Contains the `Lexer`, `Parser`, and `SemanticPass`. Its sole purpose is to transform strings into validated bytecode.
- **Runtime (The Virtual Machine):** The `VextVM` operates exclusively on bytecode. It manages the execution stack, variable storage, and native module hooks.
- **Vext.Shared:** The bridge between the compiler and runtime. Defines the AST nodes, bytecode instructions, and the `VextValue` memory representation.
- **Tooling:** The `Vext.LSP` provides language server support for VS Code, including syntax highlighting and real-time diagnostics.

---

## 📖 Engineering Guides

Select a module below to dive into the technical details of the Vext engine.

### [1. Execution Flow](ExecutionFlow.md)
Trace the rigorous path from a source code string to a finished VM execution context. Understand the interaction between the `VextEngine` and the `RuntimeEngine`.

### [2. Compiler Pipeline](Architecture/CompilerPipeline.md)
Explore the recursive descent parser, lexical analysis, and the complex `SemanticPass` that handles type inference and constant folding.

### [3. Bytecode & Virtual Machine](Architecture/BytecodeAndVM.md)
Deep dive into the Vext Instruction Set Architecture (ISA), memory layouts, and the high-performance execution loop.

### [4. Shared Data Structures](Architecture/SharedDataStructures.md)
Learn about the foundational types like `VextValue`, which uses explicit memory layouts to minimize GC pressure.

### [5. Developer Guidelines](Contributing/StyleGuide.md)
Review the mandatory C# coding standards and contribution rules for the Vext project.

### [6. Tooling & LSP](Tooling/LanguageServer.md)
Details on the Language Server Protocol implementation and the VS Code extension architecture.

---

> [!TIP]
> Always verify changes against the `Vext.Shared/Rules/LanguageSpecs.cs` file, which serves as the ultimate source of truth for keywords and operator precedence.

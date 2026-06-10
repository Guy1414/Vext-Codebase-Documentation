# Vext Codebase Documentation

This documentation details the internal C# architecture and implementation of the Vext language. It is intended strictly for developers maintaining or contributing to the core compiler, virtual machine, and associated Language Server Protocol (LSP) tooling.

---

## Activity

![Alt](https://repobeats.axiom.co/api/embed/517c0827080509d5693e453fe61a755707fb14d2.svg "Repobeats analytics image")

---

## Solution Structure

The Vext solution is physically partitioned into several .NET projects to enforce strict architectural boundaries:

*   **`Vext.Shared`** (Class Library)
    *   **Role**: The universal dependency. It defines the data structures and interfaces that all other projects communicate with.
    *   **Contents**: `AST` nodes, `VextValue` struct, `VextVMBytecode` enum, `Token` definitions, and the `Module` base classes.
*   **`Vext`** (Class Library)
    *   **Role**: The Compiler. Depends *only* on `Vext.Shared`.
    *   **Contents**: Lexical analysis (`Lexer`), parsing (`Parser`), semantic validation (`SemanticPass`), bytecode generation (`BytecodeGenerator`), and the `Diagnostic` reporting system.
*   **`Runtime`** (Class Library)
    *   **Role**: The Execution Engine. Depends *only* on `Vext.Shared`. It crucially has zero dependencies on `Vext` (the compiler).
    *   **Contents**: The stack-based virtual machine core (`VextVM`) and the bootstrapping orchestrator (`RuntimeEngine`).
*   **`Vext.LSP`** (Console Application)
    *   **Role**: Host environment for VS Code. Depends on `Vext`, `Runtime`, and `Vext.Shared`.
    *   **Contents**: A persistent JSON-RPC server listening on `stdin`/`stdout` that maps compiler `Diagnostic` and `SemanticToken` outputs to editor features.
*   **`Vext.TestRunner`** (Console Application)
    *   **Role**: Automated integration testing. Depends on `Vext`, `Runtime`, and `Vext.Shared`.
    *   **Contents**: A lightweight host that compiles Vext code strings and asserts the resulting `RuntimeOutput` intercepts against expected strings.

## Documentation Index

The following documents dissect the specific C# implementations of the core systems:

*   **[Execution Flow](ExecutionFlow.md)**: A step-by-step C# method call trace from source code string to evaluated state.
*   **[Compiler Pipeline](Architecture/CompilerPipeline.md)**: Explore the recursive descent parser, lexical analysis, and the complex `SemanticPass` that handles type inference and constant folding.
*   **[Bytecode & Virtual Machine](Architecture/BytecodeAndVM.md)**: Deep dive into the Vext Instruction Set Architecture (ISA), memory layouts, and the high-performance execution loop.
*   **[Shared Data Structures](Architecture/SharedDataStructures.md)**: Learn about the foundational types like `VextValue`, which uses explicit memory layouts to minimize GC pressure.
*   **[Developer Guidelines](Contributing/StyleGuide.md)**: Review the mandatory C# coding standards and contribution rules for the Vext project.
*   **[Tooling & LSP](Tooling/LanguageServer.md)**: Details on the Language Server Protocol implementation and the VS Code extension architecture.

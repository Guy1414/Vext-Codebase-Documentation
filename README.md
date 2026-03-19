# Vext Codebase Documentation

This documentation details the internal C# architecture and implementation of the Vext language. It is intended strictly for developers maintaining or contributing to the core compiler, virtual machine, and associated Language Server Protocol (LSP) tooling.

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

*   **[Architecture](Architecture.md)**: System boundaries, static vs. dynamic project linkage.
*   **[Compiler](Compiler.md)**: Detailed breakdown of the frontend pipeline C# classes.
*   **[Runtime](Runtime.md)**: Implementation details of the stack-based VM loop and C#-to-Vext module interop.
*   **[Execution Flow](ExecutionFlow.md)**: A step-by-step C# method call trace from source code string to evaluated state.
*   **[Data Structures](DataStructures.md)**: Deep dive into the primary memory allocations and layouts (`VextValue` `StructLayout`, AST inheritance).

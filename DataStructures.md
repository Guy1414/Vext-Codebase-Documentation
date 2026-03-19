# Internal Data Structures

This document covers the C# implementation patterns of the heavily hit central data structures managing memory stability during parsing and execution phases.

## 1. `VextValue` Memory Architectonics (`Vext.Shared/Runtime/VextValue.cs`)

`VextValue` is the foundational primitive for all language types. By default across a dynamically-typed execution tree, switching variables randomly among objects severely spikes heap allocations impacting Garbage Collection sweeps randomly during VM execution.

This is fundamentally solved using a standard C#-native `enum` tag tied to a strict C-Union imitation model utilizing `StructLayout(LayoutKind.Explicit)`:

```csharp
[StructLayout(LayoutKind.Explicit)]
public struct VextValue
{
    // A 4-byte standard struct type tracker defining the target read block.
    [FieldOffset(0)] public VextType Type; 
    
    // An 8-byte overlap. The primitive representations identically layer overlapping the identical physical memory payload to totally bypass standard reference framing.
    [FieldOffset(8)] public long AsInt; 
    [FieldOffset(8)] public double AsFloat;
    [FieldOffset(8)] public bool AsBool;
    
    // Explicit 8-byte reference allocations. Allocated natively to the heap when strings are necessitated.
    [FieldOffset(16)] public string? AsString; 
}
```
*   **Instantiation Speed**: Assigning numeric values via `new VextValue { AsInt = x, Type = VextType.Int }` invokes absolute minor allocations entirely within fixed CPU stack thresholds skipping reference arrays. 

## 2. Tokens (`Vext.Compiler.Lexing.Token`)

A rigid structure instantiated constantly during string ingestion. Trackers define line/col coordinates enabling accurate telemetry mapping arrays back into Visual Studio Code `SemanticToken` API boundaries via the Language Server.

```csharp
public class Token
{
    public TokenType Type { get; }
    public string Value { get; }
    public int StartLine { get; }
    public int StartCol { get; }
    public int EndLine { get; }
    public int EndCol { get; }
}
```

## 3. The AST Polymorphic Hierarchy (`Vext.Shared.AST`)

The `AST` logic leverages rigid classical Inheritance maps separating statement declarations lacking output vs generic numerical expressions actively modifying stacks.

*   `ASTNode` (Abstract Base)
    *   Properties: `Line`, `Column` metrics mapping to origin `Token` boundaries.
    *   `StatementNode`
        *   Children: `ExpressionStatementNode`, `VariableDeclarationNode`, `IfStatementNode`, `WhileStatementNode`.
            *   Execution translates dynamically mapped code block groupings evaluating without pushing final resultant structural items to the central array parameter stack.
    *   `ExpressionNode`
        *   Children: `LiteralNode`, `BinaryExpressionNode`, `FunctionCallNode`, `VariableAccessNode`.
            *   Execution translates explicitly onto postfix operation stack elements directly returning evaluated constants mapped to `VextValue` implementations inside `BytecodeGenerator.EmitExpression()` recursive sweeps.

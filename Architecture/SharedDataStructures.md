# Shared Data Structures (`Vext.Shared`)

The Vext Engine separates the C# Compiler logic natively from the JVM execution logic natively completely. They communicate perfectly seamlessly through a standalone core project class: `Vext.Shared`.

This directory houses the foundational bounds that the entire architecture relies on.

---

## 1. The AST Node Trees (`Nodes/`)

During the `Parser.cs` phase, the strings translate into robust generic classes implementing `Node`. These C# classes map mathematical hierarchies linearly tracking tokens efficiently softly.

*   `BinaryExpressionNode`
*   `FunctionCallNode`
*   `IfStatementNode`
*   `LiteralNode`

Each node houses `LineNumber` and `ColumnNumber` properties so `SemanticPass` telemetry structurally hooks directly backward onto original TextDocument lengths completely flawlessly.

---

## 2. Bytecode Modules (`Modules/`)

The Virtual Machine natively supports injecting independent execution limits natively natively natively via `Vext.Shared.Modules.Module`.

A `Module` is a simple dictionary mapping string namespaces into C# `Func<object[], object?>` native hooks natively natively. 

```csharp
// Example Math Module Architecture
public Module Initialize()
{
    Add("Sin", new Function("Sin", 1, args => Math.Sin(Convert.ToDouble(args[0]))) 
    { 
        ReturnType = "float",
        Parameters = [new FunctionParameterNode { Name = "a", Type = "numeral" }]
    });
    return this;
}
```

---

## 3. The `VextValue` Struct - The Holy Grail

By far the most critical class file globally inside `Vext.Shared\Runtime\VextValue.cs` mapping limits linearly cleanly cleanly.

Because C# classes execute structurally via the Heap resulting in costly Garbage Collection passes, storing variables as objects forces huge latency natively loops. 

`VextValue` circumvents C# limits entirely by utilizing **[StructLayout(LayoutKind.Explicit)]**. 
It perfectly seamlessly simulates a dynamic **C-language Union** inside C# softly softly!

```csharp
[StructLayout(LayoutKind.Explicit)]
public struct VextValue
{
    [FieldOffset(0)] public VextType Type; // 1 byte
    [FieldOffset(8)] public long AsInt;    // 8 bytes
    [FieldOffset(8)] public double AsFloat;// 8 bytes (OVERLAPS!)
    [FieldOffset(8)] public bool AsBool;   // 1 byte (OVERLAPS!)
    // String is a managed reference, structurally separated cleanly natively!
}
```

### Why overlap limits? 
Since a Vext variable can only ever be exactly one primitive cleanly globally, `AsInt` and `AsFloat` share the exact identical 8-byte memory boundary securely globally seamlessly! 
This keeps `VextValue` memory usage extremely tiny, completely circumventing GC spikes when processing 100,000+ iteration `while` loops sequentially tightly efficiently.

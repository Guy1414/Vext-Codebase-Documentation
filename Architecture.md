# Deep Architectural Architecture

The Vext language solution physically segments semantic structural analysis from operative loop simulation to guarantee memory safety during language server instantiation.

## Strict Dependency Disassociation

The foremost rule of the Vext C# solution is that `Vext` (The Compiler Assembly) and `Runtime` (The Evaluation Engine Assembly) do not reference each other. They communicate purely through standardized immutable `Instruction` vectors defined inside `Vext.Shared`.

### Why This Architecture Exists
1.  **Editor telemetry**: The VS Code Language Server initiates compilation parses instantly upon every keystroke. By isolating `Vext` completely from the `Runtime`, the compiler is physically incapable of executing destructive scripting blocks (like infinite loops or unauthorized disk wiping) during editor auto-completion parsing. 
2.  **Plugin Inversion of Control**: Because `Vext.Shared.Modules.Module` defines the native signature interface, the `SemanticPass` can statically check parameter allocations universally, and `RuntimeEngine.cs` can later map those parameter logic blocks into dynamic runtime execution allocations seamlessly without compilation mapping dependencies natively. 

## The `VextValue` Struct Hack
Memory allocations severely handicap C# loops globally. To simulate dynamic bindings natively without instantiating generic `object` tags explicitly (which triggers constant Garbage Collection), the `Vext.Shared` library utilizes a Struct Layout technique simulating a C Union.

```csharp
[StructLayout(LayoutKind.Explicit)]
public struct VextValue
{
    // Evaluates mapping targets dynamically during dispatch
    [FieldOffset(0)] public VextType Type; 
    
    // An 8-byte overlapping memory block.
    // Assigning '.AsInt' or '.AsFloat' manipulates the exact same memory bits.
    [FieldOffset(8)] public long AsInt; 
    [FieldOffset(8)] public double AsFloat;
    [FieldOffset(8)] public bool AsBool;
    
    // A secondary memory heap reference (string) safely segregated.
    [FieldOffset(16)] public string? AsString; 
}
```

This ensures the VM `stack` is natively passed by value locally without triggering dynamic allocation garbage checks universally.

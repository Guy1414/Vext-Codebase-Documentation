# Vext C# Style Guide

To maintain a consistent, highly readable, and predictably structured codebase, all core contributors to the Vext Compiler (`Vext`), Virtual Machine (`Runtime`), and peripheral tooling MUST adhere to the following C# formatting and structural conventions.

We rely on a strict set of structural aesthetics derived from C# standards but tailored slightly for internal compiler readability.

---

## 1. Bracing and Indentation (Allman Style)

The Vext codebase heavily utilizes the **Allman style** for braces. Braces must always be placed on a new line for all classes, methods, namespaces, and standard control blocks.

```csharp
// CORRECT
public class Lexer
{
    public void Advance()
    {
        if (currentIndex >= vextCode.Length)
        {
            return;
        }
    }
}

// INCORRECT
public class Lexer {
    public void Advance() { ... }
}
```

### 1.1 Single-Line Branch Exceptions
For simple operations, `if/else` statements may completely omit braces, provided the execution block remains exceptionally brief and visually distinct.

```csharp
// CORRECT
if (char.IsDigit(current))
    tokens.Add(ReadNumber());
else if (char.IsLetter(current))
    tokens.Add(ReadIdentifierOrKeyword());
```

---

## 2. Instantiation (Explicit Typing & Collection Expressions)

We prioritize readability over brevity when instantiating objects. Avoid C# 9 target-typed `new()` expressions. Instead, explicitly state the class name for clarity. For standard collections, you may use C# 12 collection expressions (`[]`).

```csharp
// CORRECT
List<Token> tokens = new List<Token>();
RuntimeOutput output = new RuntimeOutput(writer, reader);
Dictionary<string, object> functions = [];

// INCORRECT
List<Token> tokens = new();
```

---

## 3. Class Definitions (Primary Constructors)

For classes that simply inject state or dependencies upon initialization, leverage **Primary Constructors** to reduce structural boilerplate. 

```csharp
// CORRECT
internal class Lexer(string vextCode)
{
    private readonly string vextCode = vextCode;
    // ...
}

// INCORRECT
internal class Lexer
{
    private readonly string vextCode;

    public Lexer(string vextCode)
    {
        this.vextCode = vextCode;
    }
}
```

---

## 4. Enhanced Pattern Matching

Whenever complex mapping operations are performed (e.g., determining mathematical opcodes or rendering error boundaries), prioritize robust `switch` expressions over chained `if/else` checks or traditional `switch` blocks. This ensures return paths are absolutely validated by the C# compiler.

```csharp
// CORRECT
VextValue res = instr.Op switch
{
    VextVMBytecode.ADD => VextValue.FromInt(lInt + rInt),
    VextVMBytecode.SUB => VextValue.FromInt(lInt - rInt),
    VextVMBytecode.MUL => VextValue.FromInt(lInt * rInt),
    _ => throw new Exception($"Unhandled numeric op")
};
```

---

## 5. Naming Conventions

Maintain strict naming boundaries directly correlating with visibility scope:
- **Private Fields:** `camelCase` (e.g., `currentIndex`, `currentLine`, `stack`). **Do NOT prefix with underscores** (`_currentIndex`).
- **Public Properties:** `PascalCase` (e.g., `Op`, `Arg`, `Target`).
- **Public Methods/Functions:** `PascalCase` (e.g., `Tokenize()`, `Run()`, `SkipTrivia()`).
- **Parameters:** `camelCase`.

---

## 6. Memory and Span Conventions

The Vext virtual machine loop heavily utilizes `Span` tracking algorithms to achieve 0 allocation array loops. 
- Use `ReadOnlySpan<T>` when parsing structural instructions natively in `VextVM`.
- Avoid instantiating new arrays inside loop cycles. Reference limits (`ref int sp`) aggressively to maintain pointer control.

```csharp
// CORRECT
ReadOnlySpan<Instruction> code = CollectionsMarshal.AsSpan(instructions);
while (ip < code.Length) {
    ref readonly Instruction instr = ref code[ip];
}
```

# Compilation Architecture

The Vext language engine splits language evaluation between two entirely distinct architectural bounds: **The Compiler** and **The Virtual Machine**. 

If you want to contribute to the way logic structurally behaves at a syntactical level, you will operate entirely within the Vext C# project encompassing the Lexer, Parser, and Semantic Pass.

## Core Philosophical Separation

The Compiler strictly transforms strings into validated byte arrays structure (specifically `List<Instruction>`). The Compiler is absolutely forbidden from maintaining state over long periods, nor is it allowed to "run" any operations. It evaluates trees.

---

## 1. The Lexical Array (`Lexer.cs`)

Raw strings (`UTF-8`) are first piped directly into `Lexer.Tokenize()`. 
- **Core Loop Check:** The Tokenizer skips trivial blocks (newlines if unmatched to statements, comments `//`, whitespace).
- **Extraction:** Characters are funneled into `ReadIdentifierOrKeyword`, `ReadNumber`, `ReadString`, and `ReadOperator`.
- **Diagnostics:** The exact `currentLine` and `currentColumn` arrays are pinned tightly to every token generated to preserve 100% mapping capability to the user's IDE editor.
- **Output:** `List<Token>`

---

## 2. Recursive Descent Parsing (`Parser.cs`)

Vext's AST construction is built dynamically using a custom, hand-written Recursive Descent mechanism that heavily respects expression limits.

### Order of Operation Parsing Layers
Instead of generic expression reading, functions explicitly fall through based on priority limits:
1. `ParseExpression()` (Lowest priority)
2. `ParseLogicalOr()`
3. `ParseLogicalAnd()`
4. `ParseEquality()`
5. `ParseComparison()`
6. `ParseTerm()` (`+`, `-`)
7. `ParseFactor()` (`*`, `/`, `%`)
8. `ParsePower()` (`**`)
9. `ParsePrimary()` (Identifiers, literals, brackets)

### Syntactic Recovery (`RecoverStm()`)
Because the Vext compiler serves the LSP natively, the Parser employs structural recoveries to prevent total compiler failures on trailing brackets. Instead of halting completely, the engine forces the scanner to advance until resolving the nearest semicolon `;`, permitting later code blocks to continue evaluating correctly.

---

## 3. The Semantic Verification Bridge (`SemanticPass.cs`)

By far the most complicated infrastructure class in the engine, `SemanticPass` does not just "generate Bytecode" `List<Instruction>` natively. It strictly evaluates scopes and enforces absolute path executions natively using standard C# Stack techniques.

### Algorithmic Scoping
Variable tracking occurs inside an explicitly constrained `Dictionary<string, int> varIndices` holding exactly 64 primitive entries to match VM memory boundaries natively natively. 

To govern block shadowing (e.g., stopping an `if` statement from assigning variables leaking outwardly), the Compiler uses a high performance `Stack<BitArray> assignedSlots` check limit natively. Pushing scopes generates shallow bounds masking existing integer ids.

### Validating Guaranteed Output execution Native Paths
For `UserFunction`, the Compiler validates exactly if a specific block yields a defined exit native hook leveraging `CheckReturnPath`. If returning structurally `int`, every single logical trailing boundary check sequence evaluates branches recursively enforcing safety guarantees.

### Type Folding Checks
Semantic pass limits overhead significantly by statically calculating numeric literal combinations simultaneously before releasing bytecodes to the VM structurally. 

```csharp
// Fold structural limits in SemanticPass natively natively 
if (op == "+" && left is LiteralNode { Type: "int", Value: long l2 } &&
                 right is LiteralNode { Type: "int", Value: long r2 }) {
    // Generate raw primitive literal node replacing mathematical syntax expression natively natively!
}
```

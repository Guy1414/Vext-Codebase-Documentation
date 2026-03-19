# Deep Compiler Internals

The `Vext` namespace parses arrays of character logic mapping AST trees natively to `VextVMBytecode`.

## 1. Syntax Parsing & Recovery Algorithms (`Parser.cs`)
The parsing structure isolates grammar evaluations implementing a classic top-down Recursive Descent mechanism tracking a flat integer `_current` iteration index upon the Lexical token bounds.

### Tricky Edge Case: Statement Synchronization Recovery
Instead of catching generic logical failures globally generating a single error per execution, the parser organically catches `Exception` blocks explicitly. Upon detecting failure bounds, it triggers `RecoverToStatementBoundary()`. 
This function dynamically evaluates `while` discarding tokens organically until it reaches a generic `;` delimiter or a control flow block `}` mapping delimiter, gracefully yielding evaluation to continue hunting subsequent syntactic flaws locally.

## 2. Semantic Slot Tracking & Scoping
`SemanticPass.cs` implements an implicit multi-pass validation architecture natively mapped on the abstract tree definitions globally.

### Variable Array Slot Architecture
To avoid looping `Dictionary<string, VextValue>` name checks during fast execution simulations, the `SemanticPass` maintains a tracking index mapping variable identifiers sequentially to an integer target. Every local variable generates a strict native `[Index]` tag identifying its explicit flat-array position logic dynamically globally.

### Block Scoping BitArrays
The biggest hurdle natively parsing dynamically generated blocks globally assigns control flow validation explicitly mapping paths. The `SemanticPass` pushes `new Dictionary<string, VariableInfo>` layers mapping every `{` bracket, checking definitions structurally pushing limits, globally preventing shadowing logic boundaries explicitly. Tracking branch paths pushes bounds to a `Stack<BitArray>`.

## 3. LSP Telemetry Hooks
As the compiler parses definitions naturally, the `SemanticPass` concurrently tracks coordinates mapping tokens visually pushing `List<SemanticToken>`.
VS Code needs explicit definition targets natively identifying logic explicitly.
1. Struct bounds track column line data dynamically.
2. The logic structurally assigns mapped tags defining explicitly `keyword`, `variable`, `string` etc.
3. Errors push naturally to `List<ErrorDescriptor> Diagnostic.Errors`.

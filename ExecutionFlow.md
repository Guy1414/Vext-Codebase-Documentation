# Internal Execution Call Trace

This tracks the rigorous C# method invocation path bridging a text string input to a finished virtual machine iteration context.

## 1. Initiating the Block
The Host Environment (e.g., `Program.Main` in `Vext.TestRunner`) calls `VextEngine.Compile(string sourceCode)`.

## 2. The Compiler Pipeline (`VextEngine.Compile`)
1.  **Lexical Pass**: 
    Instantiates `Lexer(sourceCode)`. Calls `Lexer.Tokenize()`. Receives a `List<Token>`.
2.  **Parser Build**: 
    Instantiates `Parser(tokens)`. Calls `Parser.Parse()`. Iteratively executes `ParseStatement()` creating instances of `ASTNode`. Receives `List<StatementNode>`.
3.  **Semantic Analytics**: 
    Instantiates `SemanticPass()`. Iterates mapping via `SemanticPass.Analyze(node)`. 
    Recursively descends nodes, invoking `AnalyzeExpression()` and `Fold()`. Populates `List<SemanticToken>` and generic string array of `UsedModules`.
4.  **Error Check Break**: 
    If `Diagnostic.Errors.Count > 0`, immediately abort and wrap payload.
5.  **Instruction Map Generation**:
    Instantiates `BytecodeGenerator()`. Iterates nodes via `BytecodeGenerator.Generate(node)`. Methods like `EmitStatement()` translate tree nodes to `Instruction` instantiations tracking `VextVMBytecode`. Receive `List<Instruction>`.
6.  **Yielding State**:
    All arrays are packaged into a read-only `CompilationResult` record containing instructions and variable dictionary mappings. Returned to the host environment.

## 3. Discharging the Pipeline (`RuntimeEngine.Run`)
The Host Environment requests execution, taking `CompilationResult` instructions and passing them to `RuntimeEngine.Run(...)`.
1.  **Environment Sync**:
    `RuntimeEngine` constructs internal `Dictionary<string, Module>`.
2.  **Module Load**:
    Iterates over the compilation `UsedModules` payload strings, instantiating standard C# models like `IOModule` or `MathModule`.
3.  **IO Bootstrapping**:
    Instantiates a custom `ConsoleOutput` wrapper matching text streams required (`Console.Out`, `Console.In`) to satisfy standard `IOModule` constructor dependency injection.
4.  **VM Allocation**: 
    `VextVM` is instantiated. Memory arrays (`stack` and `variables`) are heavily pre-allocated.

## 4. Hardware Loop Simulation (`VextVM.Run`)
1.  **Iterative Advance**: 
    The `Execute()` method triggers an internal `while(ip < code.Length)`.
2.  **Instruction Decode**:
    The native enum `Instruction.Op` invokes specific `switch` blocks.
3.  **State Mangle**:
    Primitive array manipulators like `stack[--sp]` are invoked. Loop boundaries alter the execution index `ip` based on branch comparison results natively checked globally on the primitive instruction struct tags.
4.  **Yield Return**: 
    Execution breaks the loop organically at termination length. Array bounds memory captures `variables` and relays string logs tracking execution duration back up to `RuntimeEngine`, eventually yielding the entire closure to the parent application.

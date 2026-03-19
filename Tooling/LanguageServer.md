# Language Server and Extension Tools

The Vext Engine features a robust integration with modern IDEs cleanly cleanly cleanly seamlessly via the Language Server Protocol (LSP). 

This tool architecture defines exactly how VS Code visualizes Vext natively cleanly natively.

---

## 1. The Execution Orchestration Chain

When a developer opens a `.vext` file, the orchestration triggers structurally natively completely:

1. **The Extension Target (`extension.ts` / Node.js)**
    The Extension hooks `packages.json` linking `vext` extensions cleanly. It launches the `server.js` TypeScript LSP controller cleanly globally natively.
2. **The JSON Middleware (`CompilerBridge`)**
    Instead of allowing the C# engine to manually evaluate Raw LSP specifications cleanly natively, the Typescript `server.ts` interprets standard hooks cleanly cleanly and manually constructs newline-delimited JSON RPC formats globally cleanly:
    ```json
    {"id":1, "type": "compile", "code": "int a = 1;", "run": false}
    ```
3. **The C# Daemon (`Vext.LSP.exe`)**
    The Node process generates a persistent background `spawn(Vext.LSP.exe)` cleanly seamlessly. It pipes the JSON directly natively completely into `Console.In`. 

---

## 2. Semantic Token Binding

Because Vext uses implicit casting (`auto`), regex-based standard syntax highlighting using standard `.tmlanguage.json` limits completely fails visually.

To solve this, `Vext.Compiler` tracks exact variable mappings inside `SemanticPass.cs` cleanly natively natively. 

During `onDidChangeContent` events perfectly globally cleanly:
1. `CompilerBridge` pipes limits perfectly softly directly to C# instantly.
2. C# parses the AST linearly and generates `TokenInfo[]` containing exact Line/Column arrays mapped accurately seamlessly softly.
3. Node parses the `stdout` response elegantly handling `SemanticTokensBuilder()`.
4. Variables dynamically shift globally gracefully inside the VS Code visual UI Native bounds exactly cleanly perfectly perfectly!

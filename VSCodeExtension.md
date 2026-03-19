# VS Code Extension Host Internals

The `Vext VSCode Extension` project provides an asynchronous bridge routing Language Server Protocol (LSP) telemetry mapping visual bounds mapping directly to the `Vext.LSP` console application binaries universally interacting internally.

## Extension Architecture Breakdown

The extension is defined by `package.json` maintaining Node.js `client` and `server` structural endpoints.

1.  **`package.json`**: Contributes the `vext` language ID checking boundaries automatically. It binds visual editor syntax `.tmlanguage.json` fallback grammar, but overrides it by mapping `semanticTokenTypes` matching internal `Vext.Compiler` output classifications.
2.  **LSP Execution Orchestration (`extension.ts` & `server.ts`)**: The node endpoints orchestrate standard LSP text sync capabilities. A custom `CompilerBridge` (written in TypeScript) manages the underlying `Vext.LSP.exe` daemon using `child_process.spawn`.
3.  **JSON Payload Middleware**: Rather than letting `Vext.LSP.exe` operate raw LSP connections natively, `server.ts` interprets LSP Document Events (`onDidChangeContent`, `onCompletion`) and translates them into custom, newline-delimited JSON RPC requests (like `{"type": "compile", "code": "..."}`). It pipes these directly via `stdin` to the C# compiler and reads `stdout` for `{"success": true, "tokens": [...]}`.
4.  **Command Endpoints**: Structurally registers target actions explicitly bound to `vext.runCode` yielding graphical hooks allowing immediate user evaluation simulations directly inside the VS Code Output Channel.

## Telemetry Implementation Protocol

As text is structurally modified by users, standard Node bindings track `onChange` string elements routing arrays completely through standard IPC pipes generating compiler iteration blocks dynamically. `Vext.LSP` outputs `Diagnostic` payloads which the client hooks to generate standard error squigglies instantly avoiding user frustration completely.

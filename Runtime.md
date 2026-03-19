# Deep Runtime Pipeline Specifications

The `Runtime` dynamically acts exclusively upon `Instruction` parameter sequences defining logic without context.

## The Dispatch Control Loop
`VextVM.cs` relies upon standard fixed C# allocations avoiding generic list resizing. It explicitly instantiates bounds:
1. `VextValue[] stack = new VextValue[2048];`
2. `VextValue[] variables = new VextValue[256];`

The `Run()` instance iteratively triggers `switch (instruction.Op)` evaluating `VextVMBytecode` locally via iteration limits.

### Stack Manipulations
Executing a `BinaryAddition` logic tree tracks:
1. `long b = stack[--sp].AsInt;`
2. `long a = stack[--sp].AsInt;`
3. `stack[sp++] = new VextValue { AsInt = a + b, Type = VextType.Int };`

The native tag bypasses standard evaluation methods entirely, keeping bounds evaluation inside generic CPU stack blocks.

## Function Evaluation Mechanics
When evaluating custom user bounds inside global methods explicitly calling `CALL_NATIVE`:

### The Boxing Penalty Intercept
Vext attempts mitigating object boxing implicitly entirely. However, because user modules rely on native C# interoperability, standard method endpoints map dynamically into `Func<List<object>, object>`.
The `ExecuteCall` block must therefore temporarily map primitive `VextValue` arrays sequentially boxing elements dynamically into object headers (`object boxed = param.AsInt;`).

Once execution returns a C# object, execution traps dynamic type checks verifying mapping parameters safely collapsing logic into generic specific explicit `VextValue` blocks universally ensuring safety dynamically.

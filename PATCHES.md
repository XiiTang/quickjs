# Boundless patches

This fork carries the patches Boundless needs on the `boundless` branch; `master`
mirrors upstream. Boundless pins it through its `XiiTang/rquickjs` fork.

## Keep the stack guard on WASI

Upstream disables `JS_SetMaxStackSize` when built for `__wasi__`, so a script that
recurses without end runs until it exhausts the WebAssembly shadow stack and
traps the whole instance. Boundless runs QuickJS inside a wasm instance and sets
a maximum stack below the instance's own stack, so deep recursion must throw a
catchable `RangeError` instead. The patch removes the WASI special case from
`update_stack_limit`; `JS_NewRuntime2` still leaves the limit off until the
embedder sets one.

Regression: Boundless's script runtime test that recurses without end and
expects `RangeError: Maximum call stack size exceeded`.

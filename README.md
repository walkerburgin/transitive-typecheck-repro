# `transitive-typecheck-repro`

## Repro
- Run `bazel build //packages/foo-lib:foo-lib_typecheck` and observe that the buidl fails with a typechecking error:
  ```
  packages/foo-lib/index.mts(2,7): error TS2322: Type 'number' is not assignable to type 'string'.
  packages/foo-lib/index.mts(3,3): error TS2322: Type 'string' is not assignable to type 'number'.
  ```
- Run `bazel build //packages/foo-app:foo-app_transitive_typecheck` and observe that the build does **not** fail like we would expect

## Notes

I think the issue is that `aspect_rules_js` is not forwarding the `transitive_typecheck` output group through the various layers of indirection:

<img width="1702" height="59" alt="image" src="https://github.com/user-attachments/assets/267a5973-0ae9-4de3-bcdd-1cd0a433b110" />

You can see the `transitive_typecheck` output group attached to `//packages/foo-lib`:

```
➜  bazel cquery //packages/foo-lib --output=starlark --starlark:expr='providers(target)["OutputGroupInfo"]'
struct(_action_inputs = depset([<generated file packages/foo-lib/tsconfig.json>, <generated file packages/foo-lib/index.mts>]), _hidden_top_level_INTERNAL_ = depset([<generated file packages/foo-lib/index.mjs>, <generated file packages/foo-lib/index.mjs.map>]), _validation = depset([<generated file packages/foo-lib/foo-lib_params.validation>]), transitive_typecheck = depset([<generated file packages/foo-lib/foo-lib.typecheck>]), typecheck = depset([<generated file packages/foo-lib/foo-lib.typecheck>]), types = depset([<generated file packages/foo-lib/index.d.mts>, <generated file packages/foo-lib/index.d.mts.map>]))
```

And we've already lost it by `//packages/foo-lib:pkg`: 

```
➜  bazel cquery //packages/foo-lib:pkg --output=starlark --starlark:expr='providers(target)["OutputGroupInfo"]'
struct(_hidden_top_level_INTERNAL_ = depset([<generated file packages/foo-lib/package.json>, <generated file packages/foo-lib/index.mjs>, <generated file packages/foo-lib/index.mjs.map>]), _validation = depset([<generated file packages/foo-lib/foo-lib_params.validation>]), runfiles = depset([<generated file packages/foo-lib/package.json>, <generated file packages/foo-lib/index.mjs>, <generated file packages/foo-lib/index.mjs.map>]), types = depset([<generated file packages/foo-lib/package.json>]))
```

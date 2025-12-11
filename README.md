# `transitive-typecheck-repro`

Repro:
- Run `bazel build //packages/foo-lib:foo-lib_typecheck` and observe that the buidl fails with a typechecking error:
  ```
  packages/foo-lib/index.mts(2,7): error TS2322: Type 'number' is not assignable to type 'string'.
  packages/foo-lib/index.mts(3,3): error TS2322: Type 'string' is not assignable to type 'number'.
  ```
- Run `bazel build //packages/foo-app:foo-app_transitive_typecheck` and observe that the build does **not** fail like we would expect

I think the issue is that `aspect_rules_js` is not forwarding the `transitive_typecheck` information correctly when the dependency goes through `node_modules` 🤔

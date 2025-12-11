load("@aspect_rules_js//js:defs.bzl", "js_library")
load("@aspect_rules_ts//ts:defs.bzl", "ts_config", "ts_project")
load("@npm//:defs.bzl", "npm_link_all_packages")

npm_link_all_packages(name = "node_modules")

ts_project(
    name = "foo-lib",
    srcs = ["index.mts"],
    declaration = True,
    declaration_map = True,
    isolated_typecheck = True,
    preserve_jsx = False,
    source_map = True,
    tsconfig = ":tsconfig",
    visibility = ["//visibility:public"],
)

ts_config(
    name = "tsconfig",
    src = "tsconfig.json",
    visibility = ["//visibility:public"],
)

js_library(
    name = "pkg",
    srcs = ["package.json"],
    visibility = ["//:__pkg__"],
    deps = [":foo-lib"],
)

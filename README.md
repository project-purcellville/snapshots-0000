# Decompilation Snapshot Storage for `angr`

This repository is a data store of all the `angr` decompilation outputs from running the corpus test suite
in CI. The only interactions with this repository should be done through the CI tests.

### External Repository Links

Corpus File Store: https://github.com/project-purcellville/direct-file-store-0000

## Organization

The following repository mimics the directory structure from the corpus file store. These
snapshots are used for validation and acceptance around the output of the `angr` decompilation
analysis.

In order to get a reasonable diff to review, the format of these snapshot files are JSON
but with all newline `\n` chars appended with actual newline characters. This makes the
files invalid JSON, however it makes the decompilation diff manageable to review. Check below
for an example snippet.

```
{"4198400:_init": "extern struct_0 *g_406b44;\n
\n
int _init()\n
{\n
    if (g_406b44)\n
    {\n
        g_406b44();\n
        return;\n
    }\n
    return;\n
}\n
",
```

## Acceptance for New PRs

New PRs to this repo should almost always be auto generated from the GitHub Actions bot
that is running the decompilation test suite in the `angr` GitHub repo. When a new PR
is generated from the `angr` GitHub actions, it will make a link to that PR here.

The associated file diff will appear in the PR as per the usual GitHub PR process with
any changed decompilation results available to review, comment on, accept, and merge into
main. These PRs in the snapshots repo will gate the acceptance of the associated PR that
in the `angr` repo.

### Examples of observations that need to go through an acceptance test

Changes to the `angr` repository has the potential to influence the quality and
consistency of the decompilation output. Below is an example of what this might look like.

Specifically, these examples are a result of non-determinism that exists within the
`angr` decompilation analysis suite.

```c
-        v1->field_0 = ((v3 >> 31 CONCAT v3) /m 10 >> 32) + 48;\n
+        *(v1) = ((v3 >> 31 CONCAT v3) /m 10 >> 32) + 48;\n
```

```c
-            v4 = v9 + v10 * 20;\n
+            v4 = v9 + 0x14 * v10;\n
```

```c
-    unsigned long v5;  // [bp-0x3c]\n
-    unsigned long v6;  // [bp-0x34]\n
-    unsigned long v7;  // [bp-0x2c]\n
-    unsigned long v8;  // [bp-0x24]\n
-    unsigned long v9;  // [bp-0x1c]\n
-    unsigned long v10;  // [bp-0x14]\n
-    unsigned long v11;  // [bp-0xc]\n
-    int v14;  // xmm0\n
-    int v16;  // xmm0\n
-    int v21;  // xmm0, Other Possible Types: unsigned long\n
-    struct_0 *v22;  // eax, Other Possible Types: unsigned long long [6], unsigned int\n
+    unsigned long v5;  // [bp-0x2c]\n
+    unsigned long v6;  // [bp-0x24]\n
+    unsigned long v7;  // [bp-0x1c]\n
+    unsigned long v8;  // [bp-0x14]\n
+    unsigned long v9;  // [bp-0xc]\n
+    int v12;  // xmm0, Other Possible Types: unsigned long\n
+    int v14;  // xmm0, Other Possible Types: unsigned long\n
+    int v18;  // xmm0, Other Possible Types: unsigned long\n
+    struct_0 *v19;  // eax, Other Possible Types: unsigned long long [6], unsigned int\n
```

Upon reviewing any changes to the snapshot for the tested binary, if the changes are
deemed acceptable, they can be merged into main and will be used in future testing
as the new baseline.

# Introduction

This is a minimal repo for reproducing an issue I am seeing with @swc-node/register v1.10.9.

When attempting to import a library `bignumber.js` I receive the error

```
Error: All of the aliased extensions are not found for /.../swc-import-bug/package/node_modules/bignumber.js: bignumber.js cannot be resolved in file:///.../swc-import-bug/package/src/index.ts
    at resolve (file:///.../swc-import-bug/node_modules/@swc-node/register/esm/esm.mjs:157:15)
    at async nextResolve (node:internal/modules/esm/hooks:865:22)
    at async Hooks.resolve (node:internal/modules/esm/hooks:303:24)
    at async MessagePort.handleMessage (node:internal/modules/esm/worker:196:18)
```

It also appears this may be related to working within a yarn workspace where the module resolution needs to find the package in the parent directory.

## Reproduction Steps

### Node

I am using the latest v18 version of v18.20.4

```
✗ node --version
v18.20.4
```

### Install dependencies

Install dependencies using yarn in project root

```
yarn install
```

### Run the test file

```
cd package
node --loader @swc-node/register/esm --no-warnings=ExperimentalWarning src/index.ts
```

## Other notes

### Using ts-node

The file executes without issue when run via ts-node:

```
yarn run ts-node src/index.ts
yarn run v1.22.22
$ /Users/benzittlau/code/swc-import-bug/node_modules/.bin/ts-node src/index.ts
2
```

Or via the ts-node loader:

```
node --loader ts-node/esm --no-warnings=ExperimentalWarning src/index.ts
2
```

### Renaming the package

If I rename the packages folder to "bignumber.js" and modify the import to match, the script runs successfully.

```
node --loader @swc-node/register/esm --no-warnings=ExperimentalWarning src/index.ts
2
```

Consequently I suspect the issue is related to the `.js` extension and module resolution in some form.

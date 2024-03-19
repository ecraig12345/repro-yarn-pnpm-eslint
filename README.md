Demonstrating an issue with `vscode-eslint` in a repo using Yarn v4 with `pnpm` linker.

## Steps

1. Clone repo and open in VS Code
2. `yarn`
3. Open `index.js` and observe a `prefer-const` lint error
3. Open `index.ts` and observe that there's no lint error with the same code

With Yarn v4's `pnpm` linker, the realpath of `@typescript-eslint/parser` is something like `node_modules/.store/@typescript-eslint-parser-virtual-783997822c/package` as shown below. (`node_modules/@typescript-eslint/parser` also exists, but it's a symlink.)

This is a problem for the extension's [parser detection](https://github.com/microsoft/vscode-eslint/blob/8173467bfdb046468336e5b6878fdf484d69cfba/server/src/eslint.ts#L1012) because it expects the resolved parser realpath to match [`/\/@typescript-eslint\/parser\//
`](https://github.com/microsoft/vscode-eslint/blob/8173467bfdb046468336e5b6878fdf484d69cfba/server/src/eslint.ts#L755).

```
node_modules
├── .store
│   └── @typescript-eslint-parser-virtual-783997822c
│       ├── node_modules
│       │   └── @typescript-eslint
│       │       └── parser -> ../../package
│       └── package
│           ├── (contents)
│           └── package.json
└── @typescript-eslint
    └── parser -> ../.store/@typescript-eslint-parser-virtual-783997822c/package
```

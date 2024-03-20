Demonstrating an issue with `vscode-eslint` in a repo using Yarn v4 with `pnpm` linker.

## Steps

1. Clone repo and open in VS Code
2. `yarn`
3. Observe that `yarn lint` shows two errors
4. Open `index.js` and observe a `prefer-const` lint error in the editor
5. Open `index.ts` and observe that there's no lint error shown with the same code

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

Interestingly, `pnpm` itself doesn't have this problem (note the `@typescript-eslint/parser` segment at the end of the path).
```
node_modules
├── .pnpm
│   └── @typescript-eslint+parser@7.3.1_eslint@8.57.0_typescript@5.4.2
│       └── node_modules
│           └── @typescript-eslint
│               └── parser
│                   ├── (contents)
│                   └── package.json
└── @typescript-eslint
    └── parser -> ../.pnpm/@typescript-eslint+parser@7.3.1_eslint@8.57.0_typescript@5.4.2/node_modules/@typescript-eslint/parser
```
(You can demo this by removing the `packageManager` setting from `package.json`, `npm i -g pnpm`, `pnpm i`.)

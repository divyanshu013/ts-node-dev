# ts-node-dev

> Tweaked version of [node-dev](https://github.com/fgnass/node-dev) that uses [ts-node](https://github.com/TypeStrong/ts-node) under the hood.

It restarts target node process when any of required files changes (as standard `node-dev`) but shares [Typescript](https://github.com/Microsoft/TypeScript/) compilation process between restarts. This significantly increases speed of restarting comparing to `node-dev -r ts-node/register ...`, `nodemon -x ts-node ...` variations because there is no need to instantiate `ts-node` compilation each time.

## Install

![npm (scoped)](https://img.shields.io/npm/v/ts-node-dev.svg?maxAge=86400) [![Build Status](https://travis-ci.org/whitecolor/ts-node-dev.svg?branch=master)](https://travis-ci.org/whitecolor/ts-node-dev)


```
yarn add ts-node-dev --dev
```

```
npm i ts-node-dev --save-dev
```

`ts-node` dependency version is not fixed, so it will install the latest version by default.

## Usage

```
ts-node-dev [node-dev|ts-node flags] [ts-node-dev flags] [node cli flags] [--] [script] [script arguments]
```

So you just combine [node-dev](https://github.com/fgnass/node-dev) and [ts-node](https://github.com/TypeStrong/ts-node) options (see docs of those packages):

```
ts-node-dev --respawn --transpile-only server.ts
```

There is also short alias `tsnd` for running `ts-node-dev`:

```
tsnd --respawn server.ts
```

**Also there are additional options specific to `ts-node-dev`:**

- `--prefer-ts` (`--prefer-ts-exts`) - (default: false) - For each `.js` file (that is not in `node_modules`) will try to check if corresponding `.ts` version exists and require it.
- `--ignore-watch` - (default: []) - files/folders to be [ignored by `node-dev`](https://github.com/fgnass/node-dev#ignore-paths). **But also this behaviour enhanced:** it will also make up `new RegExp` of passed ignore string and check absolute paths of required files for match.
  So, to ignore everything in `node_modules`, just pass `--ignore-watch node_modules`, or us `--no-deps` for the same effect.

- `--debug` - Some additional debug output.
- `--interval` - Polling interval (ms)
- `--debounce` - Debounce file change events (ms, non-polling mode)
- `--clear` (`--cls`) Will clear screen on restart
- `--watch` - Explicitly add arbitrary files or folders to watch and restart on change (list separated by commas, [chokidar](https://github.com/paulmillr/chokidar) patterns)
- `--exit-child` - Adds 'SIGTERM' exit handler in a child process.
- `--rs` - Allow to restart with "rs" line entered in stdio, disabled by default.

**Caveats and points of notice:**

- Especially for large code bases always consider running with `--transpileOnly` flag which is normal for dev workflow and will speed up things greatly. Note, that `ts-node-dev` will not put watch handlers on TS files that contain only types/interfaces (used only for type checking) - this is current limitation by design.

- `--ignore-watch` will NOT affect files ignored by TS compilation. Use `--ignore` option (or `TS_NODE_IGNORE` env variable) to pass **RegExp strings** for filtering files that should not be compiled, by default `/node_modules/` are ignored.

- Unknown flags (`node` cli flags are considered to be so) are treated like string value flags by default. The right solution to avoid ambiguity is to separate script name from option flags with `--`, for example:

  ```
  ts-node-dev --inspect -- my-script.ts
  ```

- The good thing is that `ts-node-dev` watches used `tsconfig.json` file, and will reinitialize compilation on its change, but you have to restart the process manually when you update used version of `typescript` or make any other changes that may effect compilation results.

## Support

Support the project with a donation and help us to give it back. [[Become a backer](https://opencollective.com/ts-node-dev#backer)]

## License

WTF.

# wasmrt [![Build Status](https://travis-ci.org/yglukhov/nimwasmrt.svg?branch=master)](https://travis-ci.org/yglukhov/nimwasmrt)

Disclaimer. This is a proof of concept, use with caution.

Compile nim to wasm
```nim
import wasmrt
proc consoleLog(a: cstring) {.importwasm: "console.log(_nimsj($0))".}
consoleLog("Hello, world!")
```

```sh
nim c --out:test.wasm test.nim # Special nim config is required, see below
node tests/runwasm.js test.wasm
```

# Prerequisites
- clang 8.0 or later
- Special Nim config, like [this one](https://github.com/yglukhov/nimwasmrt/blob/master/tests/test.nims)
- [Optional] [wasm-gc](https://github.com/alexcrichton/wasm-gc) - a tool to compact your wasm file

# Run your wasm
The wasm file generated this way is pretty standalone, and requires only the following JavaScript code to bootstrap:
```js
function runNimWasm(w){for(i of WebAssembly.Module.exports(w)){n=i.name;if(n[0]==';'){new Function('m',n)(w);break}}}
```
`runNimWasm` takes the output of `WebAssembly.compile` function. E.g. to run a wasm file in nodejs, use smth like [runwasm.js](https://github.com/yglukhov/nimwasmrt/blob/master/tests/runwasm.js)

# Caveats
- Exceptions don't work.
- Nim GC is disabled on start, you have to run it carefully close to the stack bottom, otherwise it can collect live references.

# Why no Emscripten?
The goal of this project is to produce self-contained standalone wasm files from nim code, without any JS glue, or "desktop platform emulation".

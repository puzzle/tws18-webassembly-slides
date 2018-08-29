---
title: WebAssembly
---

<!--section -->
# WebAssembly

<!-- .slide: class="master01" -->

---

## WebAssembly (Wasm)

* Web standard complementary to JavaScript
* Bytecode for browsers
* Compilation target for C/C++ and other languages
* LLVM backend
* Binary and text representation

---

## WebAssembly (Wasm)

* Near native execution speed
* Sandboxing like JavaScript
* Successor to asm.js and Google NaCl
* Designed for Browsers, Mobile, IoT, ...

---

## WebAssembly MVP

* MVP in Firefox 52, Chrome 57, Edge 16, Safari 11
* MVP based on features available in todays browsers that perform well on mobile
* MVP has roughly same functionality as asm.js
* All execution environments implement the same specification
* Includes wasm32 only (32-bit)

---

## WebAssembly Roadmap

* W3C Working Group
* Thread Support
* Zero Cost Exception Support
* Garbage Collector Support
* Direct access to DOM/Web API
* SIMD Support

---

## Why is WebAssembly faster than asm.js

* Much faster parsing due to binary format
* Better support for modern CPU features
* More efficient memory access
* Toolchain improvements
* Predictable performance across browsers

---

## Emscripten

* LLVM to asm.js/Wasm compiler
* Can compile practically any portable C/C++ code
* Support for SDL and OpenGL ES 2.0
* Support for OpenAL

---

## TeaVM

* Java bytecode to asm.js/Wasm compiler
* Green Thread support
* Sophisticated optimizer
* Source Maps
* GC implemented in Java

---

## AssemblyScript

* Strictly typed subset of TypeScript
* Compiles to WebAssembly

---

## WebAssembly Types

* i32: 32-bit integer
* i64: 64-bit integer
* f32: 32-bit floating point
* f64: 64-bit floating point

Integer signed/unsigned determined by operators.

---

## WebAssembly Memory

* Sandboxed linear memory
* No aliasing
* Multiple of 64 KiB in MVP
* Accessible from JavaScript as ArrayBuffer

---

## WebAssembly Hello World

```c
// hello.c

#include <webassembly.h>

extern void printMessage(int);

export void hello() {
  printMessage(42);
}
```

---

## WebAssembly Hello World

```javascript
// hello.js

function printMessage(message) {
    document.getElementById("message").innerHTML = message
}

var importObject = {
    env: {
        memory: new WebAssembly.Memory({ initial: 2, maximum: 10 }),
        printMessage
    }
};

WebAssembly.instantiateStreaming(
    fetch('build/hello.wasm'), importObject)
        .then(module => { module.instance.exports.hello() });
```

---

## WebAssembly Hello World

```webassembly
;; hello.wasm (generated)

(module
  (type $t0 (func (param i32)))
  (type $t1 (func))
  (import "env" "printMessage" (func $env.printMessage (type $t0)))
  (import "env" "memory" (memory $env.memory 1))
  (func $hello (type $t1)
    i32.const 42
    call $env.printMessage)
  (table $T0 0 anyfunc)
  (export "hello" (func $hello))
  (data (i32.const 4) " '\00\00"))
```

---

## What to test

Look at some demos:

* https://www.funkykarts.rocks/demo.html
* https://s3.amazonaws.com/mozilla-games/ZenGarden/EpicZenGarden.html
* http://s3.amazonaws.com/mozilla-games/tmp/2017-02-21-SunTemple/SunTemple.html
* http://blog.qt.io/blog/2018/05/22/qt-for-webassembly-examples/

https://developer.mozilla.org/en-US/docs/WebAssembly

* [WebAssembly Reference Manual](https://github.com/sunfishcode/wasm-reference-manual/blob/master/WebAssembly.md)


## wasm-wheel

* https://github.com/boyanio/wasm-wheel

# mkdir build/wasm
npm install
gulp build-metadata build-wasm-folder build-wasm-assemblyscript build-wasm-c build-wasm-java build-wasm-go
gulp serve

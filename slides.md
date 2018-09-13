---
title: WebAssembly Mini Workshop
---

<!-- .slide: class="master01" -->

# WebAssembly<br>Mini Workshop

---

<!-- .slide: class="master02" -->

# Overview

---

## WebAssembly (Wasm)

* Web standard complementary to JavaScript
* Bytecode for browsers
* Compilation target for C/C++ and other languages
* LLVM backend
* Compressed binary and text representation

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

## asm.js

* Low-level subset of JavaScript
* Limited to ahead-of-time optimizable features
* Compilation target for C/C++ and other languages
* asm.js optimizations in some browser
* Performance inconsistent accross browsers

---

### Why is WebAssembly faster than asm.js?

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
* Focused on portability

---

## NodeJS WebAssembly Package

* C/C++ to Wasm compiler
* Targeting Wasm in browsers only
* Minimal boilerplate code

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

## WebAssembly Use Cases

* Improve load times of web apps
* Implement web apps in other languages than JS
* Run CPU bound workloads in browsers
* Use existing C/C++, Java, ... libraries in browsers
* Hybrid native mobile apps
* Run same binaries on different IoT devices

Also see: https://webassembly.org/docs/use-cases/

---

<!-- .slide: class="master02" -->

# Actual Code

---

## WebAssembly "Hello, World!"

<span style="display: block; text-align:left;margin: 2.5rem auto -1rem auto; width: 90%;">hello.c:</span>
```c
#include <webassembly.h>

extern void printMessage(int);

export void hello() {
  printMessage(42);
}
```

---

## WebAssembly "Hello, World!"

<span style="display: block; text-align:left;margin: 2.5rem auto -1rem auto; width: 90%;">hello.js:</span>
```javascript
function printMessage(message) {
    document.getElementById("message").innerHTML = message;
}

var importObject = {
    env: {
        memory: new WebAssembly.Memory({ initial: 2, maximum: 10 }),
        printMessage
    }
};

WebAssembly.instantiateStreaming(
    fetch('wasm/hello.wasm'), importObject)
        .then(module => { module.instance.exports.hello() });
```

---

## WebAssembly "Hello, World!"

<span style="display: block; text-align:left;margin: 2.5rem auto -1rem auto; width: 90%;">Compile:</span>
```bash
git clone https://github.com/puzzle/wasm-hello
cd wasm-hello
docker run --rm -it -p 8080:8080 -v `pwd`:/src node:8 /bin/bash
cd /src
npm install
npx gulp
```

---

## WebAssembly "Hello, World!"

<span style="display: block; text-align:left;margin: 2.5rem auto -1rem auto; width: 90%;">hello.wasm (generated):</span>
```wasm
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

<!-- .slide: class="master02" -->

# Do it Yourself

---

## Look at some Demos

* [Funky Karts](https://www.funkykarts.rocks/demo.html)
* [Epic Zen Garden](https://s3.amazonaws.com/mozilla-games/ZenGarden/EpicZenGarden.html)
* [Sun Temple](http://s3.amazonaws.com/mozilla-games/tmp/2017-02-21-SunTemple/SunTemple.html)
* [Qt for WebAssembly Examples](http://blog.qt.io/blog/2018/05/22/qt-for-webassembly-examples/)

---

## Study

* [WebAssembly Documentation](https://webassembly.org/docs/high-level-goals/)
* [Mozilla WebAssembly Documentation](https://developer.mozilla.org/en-US/docs/WebAssembly)
* [Awesome Wasm](https://github.com/mbasso/awesome-wasm)
* [Awesome WebAssembly Languages](https://github.com/appcypher/awesome-wasm-langs)
* [WebAssembly Reference Manual](https://github.com/sunfishcode/wasm-reference-manual/blob/master/WebAssembly.md)

---

## Try and Modify

* [WebAssembly "Hello, World!" in C](https://github.com/puzzle/wasm-hello)
* [WebAssembly "Hello, World!" in Java](https://github.com/puzzle/wasm-hello-java)
* [Wheel of WebAssembly](https://github.com/boyanio/wasm-wheel), also see next slide

---

## Try "Weel of WebAssembly"

```bash
git clone https://github.com/boyanio/wasm-wheel
cd wasm-wheel
docker run --rm -it -p 8080:8080 -v `pwd`:/src \
    quay.io/dtschan/wasm-wheel /bin/bash
npx gulp build-metadata build-wasm-folder build-wasm-assemblyscript \
    build-wasm-c build-wasm-java build-wasm-go
npx gulp serve
```

Then visit http://localhost:8080/.

---

## Try Emscripten "Hello, World!"

```bash
mkdir hello
cd hello
cat << EOF > hello.c
#include <stdio.h>
int main(int argc, char ** argv) {
  printf("Hello, world!\n");
}
EOF

docker run --rm -it -p 8080:8080 -v `pwd`:/src \
    trzeci/emscripten:1.38.12 /bin/bash
emcc hello.c -s WASM=1 -o hello.html
emrun --no_browser --hostname 0.0.0.0 --port 8080 .
```

Then visit http://localhost:8080/hello.html.

---

## Research

* Do the WebAssembly demos/examples run on my mobile?
* How are strings handled in the different examples?
* How does Go handle garbage collection in Wasm?
* How to extend [this Dockerfile](https://github.com/dtschan/wasm-wheel/blob/docker/docker/Dockerfile) to get the Kotlin example working

---

<!-- .slide: class="master02" -->

# Discover WebAssembly Yourself

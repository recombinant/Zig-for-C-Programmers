# Zig-for-C-Programmers
Things that aren't immediately obvious in the Zig documentation.

This assumes that the reader is competent in C/C++. These are notes, not a tutorial.

Zig is still in beta, so some of the content here may be outdated. Keeping it up to date is like nailing jell-o to a wall.

## [Zig is also a C compiler](https://ziglang.org/learn/overview/#zig-is-also-a-c-compiler)

### 1. Compiling C source to executable

#### Compiling a single C source file to executable

This just works…

`zig build-exe alice.c --library c --name wendy`

Alternatively `zig cc` can be used to invoke `clang` directly…

`zig cc -lc alice.c -o wendy`

or for Microsoft Windows

`zig cc -lc alice.c -o wendy.exe`

#### Compiling multiple C source files to a single single executable

`zig build-exe alice.c src/bob.c --library c --name wendy`

`zig cc -lc alice.c src/bob.c -o wendy`

### 2. Compiling C++ source to executable

`zig build-exe alice.cpp --library c++ --name wendyplus`

`zig cc -lc++ alice.cpp -o wendyplus`

etc.

### 3. [Zig Build System](https://ziglang.org/learn/overview/#zig-build-system)

Zig also offers a build system as an alternative to the plethora of available build systems. Only `zig` needs to be installed and understood.

The folder name determines the name of the executable name (this can easily be edited later in `build.zig`).

```bash
mkdir projectX
cd projectX
zig init-exe
```

and voilà a simple `build.zig` has been created for a `src/main.zig` sample.

`zig build` to generate the executable which will be within the folder `./zig-out/bin`

or

`zig build run` to run the exectuable without having bother to look for it.

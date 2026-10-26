# experimental

A Bazel monorepo for graphics experiments that don't belong in
[MoonBase](https://github.com/muchq/MoonBase): SDL3 and raylib demos, built with
native `cc_*` rules on a hermetic LLVM toolchain.

| Target | What |
|---|---|
| `//graphics/libs/trill` | Small SDL3 helper library |
| `//graphics/apps/mandelbrot` | Mandelbrot set explorer on SDL3 |
| `//graphics/apps/tubing` | SDL3 graphics demo |
| `//graphics/apps/raylib_example` | Hello-world raylib window |

```bash
bazel build //...
bazel run //graphics/apps/mandelbrot
```

## Dependencies

- **Toolchain:** `llvm` from the BCR: a statically linked clang and, on Linux,
  a glibc 2.28 sysroot, so nothing installed on the host is needed or used.
- **SDL3:** the BCR `sdl3` module. Its X11, Wayland and ALSA headers come from
  BCR modules too; SDL loads those libraries at runtime by soname.
- **raylib:** not in the BCR. `//third_party:extensions.bzl` fetches the 5.5
  release and `//third_party:raylib.BUILD` builds it on the BCR `glfw` module,
  as raylib's own CMake does with `USE_EXTERNAL_GLFW=ON`. Nothing links libGL:
  OpenGL 3.3 goes through raylib's bundled glad via `glfwGetProcAddress`.
- **rules_bison patch:** glfw links xkbcommon, whose parser rules_bison
  generates with a helper that calls `getenv` without `<cstdlib>`, which
  LLVM 23's libc++ no longer includes transitively.

Machine-specific flags go in `.bazelrc.user`, which is gitignored.

# Building a Luwow Project

Luwow gives full control to the user on how it should be built. Libraries can either be bound to the executable, or be their own dynamic link library files. In this page, we'll get into all of those details.

If you wish to build a Luwow project completely on your own, you can pull in the [Core](https://github.com/Luwow-Project/Core). It houses the engine, and sources for the binaries. However, in this page, we will be using the [release repository](https://github.com/Luwow-Project/Release), as it already contains everything we need for a working Luwow application. Let's begin.

## Requirements

* C++ 20 compatible compiler
* CMake 3.16 or later
* [Core](https://github.com/Luwow-Project/Core)
* [Luau](https://github.com/luau-lang/luau)

-----

## Supported Platforms

* Windows (Visual C++ compiler)
* Windows (MinGW C++ compiler)
* MacOS (Libc++ compiler)
* Linux (GNU compiler)

*(Future OS plans include iPad and Android)*

-----

## Setting Up

In a folder, run the commands below in your terminal:

### 1. Clone the Repository

```bash
git clone https://github.com/Luwow-Project/Release Luwow --recurse-submodules
cd Luwow
```

If you forget the --recurse-submodules flag, you can follow up with:
```bash
git submodule update --init --recursive
```

### 2. Configure and Build the Project

By default, Luwow can be built on the supported platforms with:

Windows:
```bash
cmake . -B build
cmake --build build --config Release
```

If using MinGW on Windows:
```bash
cmake . -B build -G "MinGW Makefiles"
cmake --build build --config Release
```

MacOS:
```bash
cmake -S . -B build -D CMAKE_BUILD_TYPE=Release
cmake --build build
```

these commands. However, the Release repository comes with a variety of options to configure how the binaries and their libraries shall be built. Below is the list that describes almost all of them:

* `-D LUWOW_BUILD_EXECUTABLES`: `ON` by default, `ON` builds the executable binaries, `OFF` does not.
* `-D LUWOW_BUILD_LIBRARIES`: `ON` by default, `ON` builds the libraries, `OFF` does not.
* `-D LUWOW_LIBRARY_MODE`: `SHARED` by default, `SHARED` builds the libraries as dynamic link libraries that can be loaded to the executable binary, while `STATIC` builds the libraries and binds them to the executable binary.
* `-D LUWOW_EXECUTABLE_TARGET`: `Luwow.RunScript` by default, the target name of the executable binary. This is used for dynamic link libraries to bind to the executable binary.

Luwow also supports 4 build type options:

* `Release`
* `Debug`
* `MinSizeRel`
* `RelWithDebugInfo`

While building, if a library supports it as an option, you can also specify which mode that library will be ran under. For more information, see [native modules](nativeModules.md).

-----

## After the Build

If the build has been completed successfully, under the build/bin/Release (Windows) or build/bin (Mac) folder, you should see these binaries:

* `compile`
* `runpackage`
* `runscript`

If `LUWOW_BUILD_LIBRARIES` is set to `ON` and `LUWOW_LIBRARY_MODE` is set to `SHARED`, a `libraries` folder will also be created alongside the binaries that contain the dynamic link libraries for each library.

-----
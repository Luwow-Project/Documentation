# Luwow Project Setup

Like it has been mentioned in the previous page, Luwow gives total control to the user on how they wish to use its components. This is why you have to select which components you wish to use, and create a project yourself using those components. 

If you don't know how to setup a Luwow project on your own however, there's already a [pre-made release repository](https://github.com/Luwow-Project/Release) that you can clone to your computer to get started. This tutorial page will go over the process of setting up this pre-made repository in detail.

## Requirements

* C++ 17 compatible compiler
* CMake 3.16 or later
* Luau source code

-----

## Supported Platforms

* Windows (Microsoft Visual Studio Code + Visual C++ compiler)
* Windows (Microsoft Visual Studio Code + MinGW C++ compiler)
* MacOS (Microsoft Visual Studio Code + XCode compiler)

*(Future OS plans include iPad, Linux, and Android)*

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

If you wish to change the config, config/build types are:
`Debug`, `Release`, `RelWithDebugInfo`, `MinSizeRel`

-----

## After Setup

If the setup has completed successfully, under the build/bin/Release (Windows) or build/bin (Mac) folder, you should see these binaries:

* `runscript`
* `runpackage`
* `runscriptwithgui`
* `remotedebug`
* `compile`

Do note that these binaries come with the release repository. If you wish, you can change, add or even remove binaries from your project. You can learn more about this in the later tutorial pages.

-----

## Usage

Each executable binary allows you to achieve certain functionality within the core Luwow engine. Let's go over them one by one:

### `runscript <script.luau>`

This executable allows you to directly compile and run a certain Luau script under the Luwow engine.

### `compile <...> <packagename.pkg>`

This executable compiles one or more Luau scripts into bytecode and places them in a package in the order listed. A package is a special file which contains the bytecode of all of the Luau scripts that are placed within. It can be used to store and run the scripts later in a compact way.

### `runpackage <packagename.pkg>`

This executable runs the first Luau script compiled within the given package under the Luwow engine. A Luau script under a package can require other scripts within the same package.

### `remotedebug <port> <script.luau>`

This executable is meant for debugging your Luau scripts under the Luwow engine. Upon running the executable, it will create a local server, which by using your IDE's debugger, can be connected to. After connecting, you should be able to see the details of your main Luau thread's stack.

### `runscriptwithgui <script.luau>`

This executable allows you to directly compile and run a certain Luau script under the Luwow engine, with support for the [GUI](https://github.com/Luwow-Project/GUI) library.

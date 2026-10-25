# Luwow Installation and Usage

## Installation

There are two ways of obtaining the binaries and the libraries for your platform to start using Luwow:

### Pre-built

If you don't want to build Luwow manually yourself to create binaries, you can head over to the [latest release](https://github.com/Luwow-Project/Release/releases/latest) to download the pre-built binaries for your platform. After the download, head over to the [usage](#usage) section to learn how to use the binaries.

### Manual

If you wish to have total control over how the binaries and their libraries are going to be built, you can check out the [building](building.md) page to get started.

## Usage

Each executable binary allows you to achieve certain functionality within the core Luwow engine. Let's go over them one by one:

### `compile [-c <.config.luau>] [-s <main.luau> <script.luau | folder> ...] [-o <output.pkg>]`

This executable compiles one or more Luau scripts into bytecode and places them in a package in the order listed. A package is a special file which contains the bytecode of all of the Luau scripts that are placed within. It can be used to store and run the scripts later in a compact way.

#### Options

* `-c <.config.luau>`: Provides a `.config.luau` configuration script to be compiled within the package. A `.config.luau` file allows you to configure how the Luwow will behave when it's being ran. More details about it can be found [here](require.md).
* `-s <main.luau> <script.luau | folder> ...`: 
    * `<main.luau>`: Provides a `.luau` file to be executed first when the package is ran. You can treat it as the entry point of your program within that package.
    * `<script.luau | folder>`: Provides either a `.luau` file or a folder path of scripts to be compiled under the package. These scripts can be ran by the main script.
* `-o <output.pkg>`: Declares how the package output will be made. Creates or overwrites a `.pkg` package file. 

### `runpackage <packagename.pkg>`

Runs the given `packagename.pkg` under the Luwow engine. Uses the `.config.luau` file compiled within the package to configure how Luwow will run.

### `runscript <script.luau> <.config.luau>`

This executable allows you to directly compile and run a certain Luau script under the Luwow engine. It accepts an optional `.config.luau` script path to configure how Luwow will run.
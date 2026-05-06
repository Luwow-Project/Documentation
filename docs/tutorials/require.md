# Require Resolution

Luwow resolves `require()` paths through three layers: **packages**, **native modules**, and **real filesystem**.
This resolution system can work differently depending on how the engine has been set-up.

## Require Path Syntax

Every require path must start with one of:

| Prefix    | Meaning                                         |
| --------- | ----------------------------------------------- |
| `@alias/` | Named alias (config-defined or built-in)        |
| `./`      | Relative to the calling file's directory        |
| `../`     | Relative to the calling file's parent directory |

Bare names like `require("foo")` are not allowed.

-----

## Resolution Order

### Alias Paths

**1. Configuration Aliases**: If the engine has been set-up with a `.config.luau` file, aliases defined within this configuration file will always win over built-in aliases. Configuration file can be given to the engine either in a compiled or a file form.

**2. Native Module Aliases**: Each native module can define its own alias to be required with. For example, `@Luwow` resolves to the native modules made by the Luwow team.

**3. `@self`**: Resolves relative to the directory containing the calling file. The exact behavior depends on context:

| Caller context                   | Resolution                                                                    |
| -------------------------------- | ----------------------------------------------------------------------------- |
| Regular file `foo/bar.luau`      | `foo/` + module path                                                          |
| `init.luau` file `foo/init.luau` | `foo/` + module path (the directory the init file represents, not its parent) |

**4. Undefined alias**: Produces an error.

### Relative paths (`./` and `../`)

Relative paths resolve from the calling file's directory. For `init.luau` files, `./` refers to the **parent** of the directory the init file sits in (i.e. the directory containing the folder that the init module represents), not the directory containing the init file itself.

-----

## Resolution for Files and Packages

Packages do not hold information about the file system beyond the project folder. This means that their resolution happens a little differently than files in certain cases.

* For relative-paths, package paths are resolved similarly to file paths, except the result will not contain information about the full filesystem path.
* For aliases, packages require a `.config.luau` file to be added during compiling. Without this information, packages cannot be used with configuration aliases.

-----

## Configuration Resolution

A configuration file (`.config.luau`) must be provided to the engine for configuration aliases to function. This can be done in two ways:

* If the engine has a compiler, then the engine will attempt to compile and run the given configuration file.
* If the engine has a package, and the package has a compiled `.config.luau` file, then the engine will attempt to just run the configuration file contents.

### Rules

* There can only be one configuration file provided to the engine.
* The engine runs the configuration file only once. The results are cached and re-used.
* The paths defined in this file are relative to the folder of the configuration file.

### Configuration File Format

```luau
return {
    luau = {
        aliases = {
            mylib = "./path/to/lib",
        },
    },
}
```

-----

## `init.luau` Semantics

A directory can act as a module by containing an `init.luau` (or `init.lua`) file. When you `require("./mydir")`, Luwow finds `mydir/init.luau`.

Inside an `init.luau` file:

- `@self` refers to the directory the init file is in (`mydir/`)
- `./` refers to the **parent** directory (the directory containing `mydir/`)
- `../` goes up from the parent directory

This means `@self/sibling` from `mydir/init.luau` resolves to `mydir/sibling.luau`, while `./sibling` resolves to `<parent>/sibling.luau`.

# Native Modules

In Luwow, to create functionality that extends the capabilities of the engine, you can use native modules. Native modules are statically registered binary modules that can be required and used from Luau.

For example, by using the [GUI](https://github.com/Luwow-Project/GUI) library, you can export GUI functionality to Luau, which allows the user to create native OS components to render:

```luau
local Gui = require("@Luwow/gui")

local Window = Gui.createWindow({
    Type = "Window",
    Title = "Luwow Created Window",
    Width = 400,
    Height = 300
})

Window.show()
```

## Creating a new Native Module

To begin creating a new native module, we'll create a new folder structure which will host our source files for our module.
For the sake of this tutorial, we'll begin with a rather easy idea, a printer module which will print the given string input to the output.

Our folder structure should look like this:

```
printer/
├── src
│   ├── Printer
│   └── PrinterRunner
```

Under the `Printer` folder, we'll have our module implementation, and under the `PrinterRunner` folder, we'll have a C++ program which binds the module to the engine for the user to use the module from Luau.

### Printer Implementation

Under the `Printer` folder, we'll create a new header file called `PrinterModule.h`, which we'll use to define our new module class.

Every module class must inherit from the `ILuauModule` class found in the [`ILuauModule.h`](https://github.com/Luwow-Project/Core/blob/main/src/Engine/ILuauModule.h) file within the engine.

#### `PrinterModule.h`

```cpp
#pragma once

#include "ILuauModule.h"
#include <string>

// We'll create a new namespace for our module under Luwow.
namespace Luwow::Printer {
    using ILuauModule = Luwow::Engine::ILuauModule;
    using Engine = Luwow::Engine::Engine;
    
    class PrinterModule : public ILuauModule { // We inherit from the ILuauModule class.
    public:
        PrinterModule();
        ~PrinterModule() = default;
    
        const char* getModuleName() const override; // Gets the module name for requiring.
        const LuauExport* getExports() const override; // Exported functions under this module.
        ILuauModule* initialize(Engine* engine) override; // Initializes the module.
        
        void printMessage(std::string message); // Our function to print the given string to the output.
    };
}
```

Next, we'll implement this class under a new C++ file called `PrinterModule.cpp`:

#### `PrinterModule.cpp`

```cpp
#include "PrinterModule.h"
#include "Engine.h"

#include "lua.h"
#include "lualib.h"

#include <iostream>
#include <string>

namespace Luwow::Printer {
    using ILuauModule = Luwow::Engine::ILuauModule;
    using Engine = Luwow::Engine::Engine;

    // For all methods that require the printer instance, we need to get it from the userdata.
    static PrinterModule* getModuleInstance(lua_State* L) {
        PrinterModule* printer = static_cast<PrinterModule*>(lua_touserdata(L, lua_upvalueindex(1)));
        if (!printer) {
            throw std::runtime_error("Printer module not found!");
        }
        return printer;
    }

    PrinterModule::PrinterModule() {}

    // Initializes a new printer module object. The engine pointer here is unused.
    ILuauModule* PrinterModule::initialize(Engine* engine) {
        PrinterModule* printer = new PrinterModule();
        return printer;
    }

    // The class method that actually provides the functionality.
    void PrinterModule::printMessage(std::string message) {
        std::cout << message << "\n"; // We print the given message to the output!
    }

    // The function that will be called from Luau, when the user calls printer.printMessage().
    static int printMessage(lua_State* L) {
        PrinterModule* printer = getModuleInstance(L); // We get the module instance from this function.
        const char* string = luaL_checkstring(L, 1); // Then we get the string argument from the stack.
        printer->printMessage(std::string(string)); // We then call the printMessage method of our printer with the given string.
        return 1;
    }

    // The name that will be used to require the module.
    const char* PrinterModule::getModuleName() const {
        return "Printer";
    }

    // The exported functions to Luau.
    static LuauExport exports[] = {
        { "printMessage", printMessage },
        { nullptr, nullptr }
    };

    // Returns the exported functions array.
    const LuauExport* PrinterModule::getExports() const {
        return exports;
    }
} // namespace Luwow::Printer
```

After implementation, our folder structure should now look like this:

Our folder structure should look like this:

```
printer/
├── src
│   ├── Printer
│   │   ├── PrinterModule.cpp
│   │   └── PrinterModule.h
│   └── PrinterRunner
```

### PrinterRunner Implementation

Now that we've completed our main printer implementation, we can begin implementing our runner which will allow us to run the engine with the printer module bound. We'll do this in a new C++ file named `main.cpp`:

#### `main.cpp`

```cpp
#include <iostream>
#include <fstream>
#include <filesystem>
#include "luacode.h"
#include "Engine.h"

#include "PrinterModule.h"

using Engine = Luwow::Engine::Engine;
using Package = Luwow::Engine::Package;

// Compiles the script from the filesystem and returns the bytecode
void compilerCallback(const std::filesystem::path& modulePath, std::string& resultingBytecode) {
    std::ifstream file(modulePath);
    if (!file.is_open()) {
        throw std::runtime_error("Failed to open script file: " + modulePath.string());
    }
    std::string script(
        (std::istreambuf_iterator<char>(file)),
        std::istreambuf_iterator<char>()
    );
    file.close();
    
    size_t bytecodeSize = 0;
    char* bytecode = luau_compile(script.c_str(), script.length(), nullptr, &bytecodeSize);
    if (!bytecode) {
        throw std::runtime_error("Failed to compile script: " + modulePath.string());
    }
    resultingBytecode = std::string(bytecode, bytecodeSize);
}

int main(int argc, char* argv[]) {
    if (argc < 2) {
        std::cerr << "Usage: runscriptwithprinter <script.luau>" << std::endl;
        return 1;
    }

    // Registers the Printer module to the engine.
    Engine::registerNativeModule(std::make_shared<Luwow::Printer::PrinterModule>());

    Engine engine((Package()), std::filesystem::path(argv[1])); // Creates the engine object with the path to the script.
    engine.setCompilerCallback(compilerCallback); // Sets the compiler callback to our callback function to compile our script.
    engine.initialize(argc, argv); // Initializes the engine with the arguments from the operating system.
    engine.run(); // Runs the engine.

    return 0;
}
```

We're almost done. For Luwow to compile and run this module alongside the engine, we need to define some `CMakeLists.txt` files.

### Defining the `CMakeLists.txt` files

We'll create two new `CMakeLists.txt` files, one under `Printer`, and another under `PrinterRunner`. We'll use these files to define how the compiler should compile our module.

#### `CMakeLists.txt` under `Printer`

```
add_library(Luwow.Printer STATIC)
target_include_directories(Luwow.Printer PRIVATE
    ${PRINTER_ROOT}/src/Printer
    ${ENGINE_ROOT}
)
target_link_libraries(Luwow.Printer PRIVATE Luwow.Engine)

target_include_directories(Luwow.Printer PRIVATE ${PRINTER_ROOT}/src/Printer)
target_sources(Luwow.Printer PRIVATE
    PrinterModule.h
    PrinterModule.cpp
)
```

#### `CMakeLists.txt` under `PrinterRunner`

```
add_executable(Luwow.RunScriptWithPrinter)
target_include_directories(Luwow.RunScriptWithPrinter PRIVATE
    ${PRINTER_ROOT}/src/Printer
    ${PRINTER_ROOT}/src/PrinterRunner
    ${ENGINE_ROOT}
)

target_include_directories(Luwow.RunScriptWithPrinter PRIVATE ${PRINTER_ROOT}/src/Printer)

target_link_libraries(Luwow.RunScriptWithPrinter PRIVATE
    Luwow.Engine
    Luwow.Printer
    Luau.Ast
    Luau.Compiler
    Luau.VM
    Luau.Common
)

set_target_properties(Luwow.RunScriptWithPrinter PROPERTIES OUTPUT_NAME runscriptwithprinter)
target_sources(Luwow.RunScriptWithPrinter PRIVATE main.cpp)
```

As you might have noticed, we use root definitions to locate our module's root folder. This definition is made inside a main `CMakeLists.txt` file, apart from the ones we have defined. This is done to make the structure more modular, and gives the user control over where the folder may be defined in the filesystem.

In our case, we expect a `PRINTER_ROOT` to be defined inside of this main `CMakeLists.txt` file. You can add this definition to your main file, using the snippet below:

```
set(PRINTER_ROOT ${LIBRARY_ROOT}/printer)
add_subdirectory(${PRINTER_ROOT}/src/Printer)
add_subdirectory(${PRINTER_ROOT}/src/PrinterRunner)
```

Finally after everything above, our new folder structure should look like this:

```
printer/
├── src
│   ├── Printer
│   │   ├── CMakeLists.txt
│   │   ├── PrinterModule.cpp
│   │   └── PrinterModule.h
│   └── PrinterRunner
│       ├── CMakeLists.txt
│       └── main.cpp
```

### Building and Running

After completing the above steps, we can now start the building process to create the `runscriptwithprinter` executable binary.

If you've been using the [release repository](https://github.com/Luwow-Project/Release), you can use the build commands from [this page](https://luwow-project.github.io/Documentation/tutorials/setup/#setting-up) to build your project. Make sure the root definitions in the main `CMakeLists.txt` file are correct.

When the building process is complete, we'll have a new binary, called `runscriptwithprinter`. We can run this binary with a path to our script to run it. Let's create an example script like so below:

```luau
local Printer = require("@Luwow/Printer")
Printer.printMessage("Hello from Luwow!")
```

And then provide the path to this script to our binary and run it:

`/path/to/runscriptwithprinter.exe /path/to/script.luau`

This should print `Hello from Luwow!` to the output.

## Conclusion

While this tutorial showed a simple example, modules can be created for all sorts of functionality. They can even be made and built for specific platforms, which we'll cover in other tutorials.




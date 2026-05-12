<h1 class="api-header" markdown>
    <span class="api-icon" markdown>:octicons-package-24:</span>
    <span class="api-title">Engine</span>
</h1>

The [`Engine`](https://github.com/Luwow-Project/Core/blob/main/src/Engine/Engine.h) object that is used to run a Luwow application.

----------------------

<!------------------------- MAIN -------------------------!-->

## initialize

```cpp
void initialize()
```

Initializes a new Luau state, sets the environment variables, and sandboxes the state.

### Returns
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr>
                    <td class="api-return-box"><a href="">void</a></td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

-----

## luaRequire

```cpp
int luaRequire(Engine* engine, lua_State* L, std::string path_str);
```

Requires a module with the given path. The path must be used according to the require-by-string semantics.

If the path string points to a Luau module, its result after execution will be saved to the cache. Further requires will return this cached result to the Luau thread it was called from.

If the path string points to a native module, its exported functions will be returned in a table to the Luau thread it was called from.

### Parameters
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr class="api-param-tr-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">engine</span>
                            <span>:</span>
                            <span class="api-param-title"><a href="">Engine\*</a></span>
                            </span>
                        </div>
                        <p class="api-param-description">The pointer to the engine object.</p>
                    </td>
                </tr>
                <tr class="api-param-tr-after-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">L</span>
                            <span>:</span>
                            <span class="api-param-title"><a href=""><span>lua_State\*</span></a></span>
                            </span>
                        </div>
                        <p class="api-param-description">The Luau thread that the function is being called with.</p>
                    </td>
                </tr>
                <tr class="api-param-tr-after-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">moduleName</span>
                            <span>:</span>
                            <span class="api-param-title"><a href=""><span>const std::string&</span></a></span>
                            </span>
                        </div>
                        <p class="api-param-description">The path string that can either point to an alias, Luau module, or a native module.</p>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

### Returns
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr>
                    <td class="api-return-box"><a href="">int</a></td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

-----

## loadModuleFromBytecode

```cpp
int loadModuleFromBytecode(lua_State* L, const std::string& moduleName, const std::string& bytecode, bool saveRef, bool useGivenState)
```

Executes a module with its bytecode in a new sandboxed Luau thread. If the engine has a debugger callback function, it will be called with the new thread state from this function.

### Parameters
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr class="api-param-tr-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">L</span>
                            <span>:</span>
                            <span class="api-param-title"><a href=""><span>lua_State\*</span></a></span>
                            </span>
                        </div>
                        <p class="api-param-description">The Luau thread that the function is being called with.</p>
                    </td>
                </tr>
                <tr class="api-param-tr-after-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">moduleName</span>
                            <span>:</span>
                            <span class="api-param-title"><a href=""><span>const std::string&</span></a></span>
                            </span>
                        </div>
                        <p class="api-param-description">The module name that represents the Luau module.</p>
                    </td>
                </tr>
                <tr class="api-param-tr-after-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">bytecode</span>
                            <span>:</span>
                            <span class="api-param-title"><a href=""><span>const std::string&</span></a></span>
                            </span>
                        </div>
                        <p class="api-param-description">The bytecode of the Luau module that will be executed.</p>
                    </td>
                </tr>
                <tr class="api-param-tr-after-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">saveRef</span>
                            <span>:</span>
                            <span class="api-param-title"><a href=""><span>bool</span></a></span>
                            </span>
                        </div>
                        <p class="api-param-description">Whether or not the engine will save the result to the cache.</p>
                    </td>
                </tr>
                <tr class="api-param-tr-after-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">useGivenState</span>
                            <span>:</span>
                            <span class="api-param-title"><a href=""><span>bool</span></a></span>
                            </span>
                        </div>
                        <p class="api-param-description">Whether or not the engine will use the given <a>lua_State\*</a> to create the new thread.</p>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

### Returns
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr>
                    <td class="api-return-box"><a href="">int</a></td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

-----

## registerNativeModule

```cpp
void registerNativeModule(std::shared_ptr<ILuauModule> module);
```

Registers a new native module to the engine. The module can then be required from Luau using its defined alias.

### Parameters
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr class="api-param-tr-first">
                    <td>
                        <div class="api-param-header">
                            <span class="api-param-title">module</span>
                            <span>:</span>
                            <span class="api-param-title"><a href=""><span>std::shared_ptr&lt;ILuauModule&gt;</span></a></span>
                            </span>
                        </div>
                        <p class="api-param-description">The pointer to the <code>ILuauModule</code> that will be registered.</p>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

### Returns
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr>
                    <td class="api-return-box"><a href="">void</a></td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

-----

## run

```cpp
void run()
```

Runs the engine. Should be called the last after initialization.

If the engine has been given a package, it will attempt to run the package contents.

If the engine has been given a Luau module with a compiler callback, it will first compile the module using the callback, and then run it.

### Returns
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr>
                    <td class="api-return-box"><a href="">void</a></td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

-----


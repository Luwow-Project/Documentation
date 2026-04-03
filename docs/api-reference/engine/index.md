<h1 class="api-header" markdown>
    <span class="api-icon" markdown>:octicons-package-24:</span>
    <span class="api-title">Engine</span>
</h1>

The `Engine` object that is used to run a Luwow application.

----------------------

<!------------------------- MAIN -------------------------!-->

```cpp
void initialize()
```

Initializes a new Luau state, sets the environment variables, and sandboxes the state.

## Returns
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
</div>

-----

```cpp
int require(lua_State* L, const std::string& moduleName);
```

Requires a module with the given name. The module name can be of a Luau module, or an internal module.

If the module is a Luau module, its result after execution will be saved to the cache. Further requires will return this cached result to the Luau thread it was called from.

If the module is an internal module, its exported functions will be returned in a table to the Luau thread it was called from.

## Parameters
<span markdown>
    <div class="md-typeset__table">
        <table>
            <tbody>
                <tr>
                    <td class="api-param-highlight">L: <a href="">lua_State*</a></td>
                    <td>The Luau thread that the function is being called with.</td>
                </tr>
                <tr>
                    <td class="api-param-highlight">moduleName: <a href="">const std::string&</a></td>
                    <td>The name of the module that will be required.</td>
                </tr>
            </tbody>
        </table>
    </div>
</span>

## Returns
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
</div>

-----


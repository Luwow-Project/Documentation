<h1 class="api-header" markdown>
    <span class="api-icon" markdown>:octicons-package-24:</span>
    <span class="api-title">Engine</span>
</h1>

The `Engine` object that is used to run a Luwow application.

----------------------

<!------------------------- MAIN -------------------------!-->

# initialize

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

# require

```cpp
int require(lua_State* L, const std::string& moduleName);
```

Requires a module with the given name. The module name can be of a Luau module, or an internal module.

If the module is a Luau module, its result after execution will be saved to the cache. Further requires will return this cached result to the Luau thread it was called from.

If the module is an internal module, its exported functions will be returned in a table to the Luau thread it was called from.

## Parameters


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


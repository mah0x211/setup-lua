# Setup Lua in GitHub Action

[![test](https://github.com/mah0x211/setup-lua/actions/workflows/test.yml/badge.svg)](https://github.com/mah0x211/setup-lua/actions/workflows/test.yml)

Setup Lua and LuaRocks in GitHub Actions.

This action supports installing any version of Lua, LuaJIT, or LuaRocks by automatically downloading (and building, if necessary) the specified version using lenv.

[lenv](https://github.com/mah0x211/lenv) is a Lua version manager that allows you to manage multiple Lua and LuaRocks installations on the same machine.


## Limitations

- Windows is not supported yet.


## Inputs


- `versions`:
  You can specify multiple pairs of Lua and LuaRocks versions, separated by space (e.g., `5.1.5:3.11.1 5.4.4:3.11.1`). The last specified pair is used in the current workflow (Default: `latest:latest`).
  - You can choose any Lua version available on the [Lua releases](https://www.lua.org/ftp/) page.
  - If you want to use LuaJIT, specify any branch from the [LuaJIT repository](https://github.com/LuaJIT/LuaJIT) with the `lj-` prefix (e.g., `lj-v2.1`).
  - When specifying LuaRocks, use the `:` separator after the Lua version (e.g., `5.1.5:3.11.1`).
- `active-version`: If you specify multiple versions in `versions`, you can select which one is activated via `active-version`. It must be one of the versions installed via the `versions` input.
- `cache`: Whether to cache the Lua and LuaRocks installation. This speeds up the installation process in subsequent runs. (Default: `true`)

**Example:**

```yaml
with:
  versions: '5.1.5:3.11.1 5.4.4:3.11.1'
  active-version: '5.1.5:3.11.1'
  cache: true
```



### Installation Location

This GitHub Action installs the tools in the following locations:

- **lenv**: Installed in the `.lenv/bin/` directory in your workspace and automatically added to `PATH`.
- **Lua**: Installed in the `.lenv/` directory in your workspace because it operates in project mode (via `lenv -p`, which installs into the local `.lenv/` directory).
- **LuaRocks**: Also installed in the `.lenv/` directory.

The action automatically sets up the following environment variables:

- **PATH**: Includes lenv's binary directory and the LuaRocks binary directory.
- **LUA_PATH**: Configured to find installed Lua modules.
- **LUA_CPATH**: Configured to find compiled C modules.

The active version of Lua creates a symbolic link in `.lenv/current/`, which sets the base directory for module references (`LUA_PATH` and `LUA_CPATH`) to `.lenv/current/lua_modules/`. This allows the runtime to correctly locate installed modules.

Since the action runs in project mode, the installation is confined to the workflow run and does not affect the global system environment. This enables you to use different versions of Lua and LuaRocks across different workflows.


## Usage

### Basic usage

The following installs Lua 5.1.5 and the latest version of LuaRocks.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - 
      name: Checkout Repository
      uses: actions/checkout@v4
    - 
      name: Setup Lua
      uses: mah0x211/setup-lua@v1
      with:
        versions: '5.1.5:latest'
    - 
      name: Run Test
      run: |
        lua -v
        luarocks --version
```

### Using in matrix builds

You can use this action in a matrix build to test multiple versions of Lua and LuaRocks. The following example demonstrates how to set up a matrix build with different versions of Lua and LuaRocks:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        lua: ['5.1.5:3.9.2', 'lj-v2.1:latest']
    steps:
    -
      name: Checkout Repository
      uses: actions/checkout@v4
    -
      name: Setup Lua ${{ matrix.lua }}
      uses: mah0x211/setup-lua@v1
      with:
        versions: ${{ matrix.lua }}
    -
      name: Run Test
      run: |
        lua -v
        luarocks --version
```


### Multiple version installations

You can specify multiple versions of Lua and LuaRocks in the `versions` input. All specified versions will be installed, but only the last one will be activated (i.e., used by the `lua` and `luarocks` commands) unless `active-version` is explicitly set.


```yaml
jobs:
  setup-test-environment:
    runs-on: ubuntu-latest
    env:
      LUA_VERSIONS: "5.1.5:3.9.2 lj-v2.1:latest"
    steps:
    - 
      name: Checkout Repository
      uses: actions/checkout@v4
    - 
      name: Setup Lua
      uses: mah0x211/setup-lua@v1
      with:
        versions: $LUA_VERSIONS
    - 
      name: Install Dependencies
      run: |
        # Setup each environment
        read -r -a versions <<< "$LUA_VERSIONS"
        for version in ${versions[@]}; do
            # you can change the version with lenv command
            echo "Switching to version: $version"
            lenv -p use "$version"

            # install dependencies
            # luarocks install ...
        done
    - 
      name: Run Test
      run: |
        # Run tests for each version
        read -r -a versions <<< "$LUA_VERSIONS"
        for version in ${versions[@]}; do
            echo "Switching to version: $version"
            lenv -p use "$version"

            # here you can run your tests 
            # ...
        done
```


## License

The scripts and documentation in this project are released under the [MIT License](LICENSE).


# Setup Lua in GitHub Action

[![test](https://github.com/mah0x211/setup-lua/actions/workflows/test.yml/badge.svg)](https://github.com/mah0x211/setup-lua/actions/workflows/test.yml)

Setup Lua and LuaRocks in GitHub Actions.


## Usage

```yaml
steps:
- uses: actions/checkout@v4
- uses: mah0x211/setup-lua@v1
    with:
    lua-version: '5.1.5'
    luarocks-version: 'latest'
- run: |
    lua -v
    luarocks --version
```

**NOTE:** Windows is not supported yet.



## Arguments

This action supports installing any version of Lua, LuaJIT, or LuaRocks by automatically downloading (and building, if necessary) the specified version using lenv.

[lenv](https://github.com/mah0x211/lenv) is a Lua version manager that allows you to manage multiple Lua and LuaRocks installations on the same machine.

| Input Name | Description | Required | Default |
|:------------|:-------------|---------|---------|
| `lua-version` | Any version available on the [Lua releases](https://www.lua.org/ftp/) page. If you want to use LuaJIT, you can specify any branch available in the [LuaJIT repository](https://github.com/LuaJIT/LuaJIT) with the `lj-` prefix (e.g., `lj-v2.1`). | No | `latest`  |
| `luarocks-version` | Any version available on the [LuaRocks releases](https://luarocks.org/releases) page. Recommended: use the `latest` version of LuaRocks. | No | `latest` |
| `cache` | Cache the Lua and LuaRocks installation. This is useful for speeding up the installation process in subsequent runs. | No | `true` |


## Installation Location

This GitHub Action installs the tools in the following locations:

- **lenv**: Installed in the `.lenv/bin/` directory in your workspace and automatically added to `PATH`.
- **Lua**: Installed in the `.lenv` directory in your workspace because it operates in project mode (using the lenv with `-p` flag).
- **LuaRocks**: Also installed in the `.lenv` directory.

The action automatically sets up the following environment variables:

- **PATH**: Includes lenv's binary directory and the LuaRocks binary directory.
- **LUA_PATH**: Configured to find installed Lua modules.
- **LUA_CPATH**: Configured to find compiled C modules.

The currently used version of Lua creates a symbolic link in `.lenv/current`, which sets the base directory for module references (`LUA_PATH` and `LUA_CPATH`) to `.lenv/current/lua_modules`. This allows the runtime to correctly locate installed modules.

Because the action runs in project mode, the installation is confined to the workflow run and does not affect the global system environment. This enables you to use different versions of Lua and LuaRocks across different workflows.

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE).


# Setup Lua in GitHub Action

Setup Lua and LuaRocks in GitHub Actions.


## Usage

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - uses: mah0x211/setup-lua@v1
      with:
        lua-version: '5.1.5'
        luarocks-version: 'latest'
    - run: lua -v
    - run: luarocks --version
```


## Inputs

#### `lua-version` (optional)

The version of Lua to set up. Default `latest`.

#### `luarocks-version` (optional)

The version of LuaRocks to set up. Default `latest`.

#### `platform` (optional)

This platform optimization option is passed to the `make` command when building `Lua` from source as follows:

```sh
make ${{ inputs.platform }}
``` 

A platform value is one of the following (default `posix`):

- on `5.1` and `5.2`:
  - `aix` `bsd` `freebsd` `generic` `linux` `macosx` `mingw` `posix` `solaris` `ansi`
- on `5.3`:
  - `aix` `bsd` `freebsd` `generic` `linux` `macosx` `mingw` `posix` `solaris` `c89`
- on `5.4`:
  - `aix` `bsd` `freebsd` `generic` `linux` `macosx` `mingw` `posix` `solaris` `c89` `linux-readline` `ios` `guess`


## License

The scripts and documentation in this project are released under the [MIT License](LICENSE)


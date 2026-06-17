# dangpkg

Package vendoring for Dang modules.

`dangpkg` reads `[[dependencies]]` from each `dang.toml`, fetches each dependency source from a git repository, and generates `vendor.gen.dang` for every Dang module that has dependencies.

## Usage

Install `dangpkg` in your Dagger workspace:

```bash
dagger install github.com/sagikazarmark/dangpkg
```

Create `dang.toml` in a Dang module directory.

Add the packages you want to vendor. For example, to vendor packages from `danglib`:

```toml
[[dependencies]]
source = "github.com/sagikazarmark/danglib"
packages = ["path", "strings"]
```

Generate `vendor.gen.dang`:

```bash
dagger generate
```

To skip Dang modules during generation, configure `excludePaths` on the module constructor:

```dang
Dagger
  .dangpkg(excludePaths: [".dagger/modules/experimental"])
  .generateWorkspace(source)
```

## License

The project is licensed under the [MIT License](LICENSE).

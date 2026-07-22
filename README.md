# dangpkg

Package vendoring for native Dang modules.

`dangpkg` reads managed entries from each `dang.toml`, resolves their sources, and synchronizes the selected package into the native import's `path`.

## Usage

Install `dangpkg` in your Dagger workspace:

```bash
dagger install github.com/sagikazarmark/dangpkg
```

Declare one package per import:

```toml
[imports.Path]
path = "./lib/path"
source = "github.com/sagikazarmark/danglib@e40600ef7e38476a51d937ef2eb1a753ff8dad88"
package = "path"

[imports.Strings]
path = "./lib/strings"
source = "github.com/sagikazarmark/danglib@e40600ef7e38476a51d937ef2eb1a753ff8dad88"
package = "strings"
```

The fields have distinct path bases:

- `path` is the destination relative to the `dang.toml` containing the import.
- `source` uses the existing dangpkg source syntax. Local paths are relative to the workspace root; GitHub and generic remote references are also supported.
- `package` selects one directory relative to the resolved source root. Use `package = "."` when `source` already identifies the package directory.

Synchronize the imports:

```bash
dagger generate
```

Dang imports are file-scoped, so each source file that uses a package must import it:

```dang
import Path
import Strings

Path.clean("./foo")
Strings.slug("Hello world")
```

The import name is explicit. It does not have to match the package directory:

```toml
[imports.Helpers]
path = "./lib/helpers"
source = "github.com/acme/danglib/lib/helpers@v1.2.0"
package = "."
```

```dang
import Helpers

Helpers.greet("world")
```

## Managed Directories

An import is managed when its table contains `source`. A native path import without `source` is left untouched:

```toml
[imports.Local]
path = "./local"
```

Dangpkg owns the complete `path` of every managed import. Synchronization removes the existing directory and recreates it from the selected package, so files that disappeared upstream and files added locally are both removed.

Each module with managed imports has a generated `.dangpkg` state file beside its `dang.toml`. It records the normalized paths managed during the previous run, allowing dangpkg to remove destinations when imports are deleted or relocated. Package directories do not contain marker files.

Only immediate `*.dang` files are copied. Files ending in `.test.dang`, nested directories, and the source package's `dang.toml` are not copied.

To update a package, change its `source` reference and run `dagger generate` again. To remove one, delete its import table and regenerate; dangpkg removes the previously managed destination.

## Local Sources

Local sources are useful in monorepos and tests:

```toml
[imports.Helpers]
path = "./lib/helpers"
source = "shared/danglib"
package = "helpers"
```

Here, `shared/danglib` is relative to the workspace root while `./lib/helpers` is relative to this `dang.toml`.

## Migration

The former `[[dependencies]]` schema is no longer supported. Split each `packages` item into its own import table:

```toml
# Before
[[dependencies]]
source = "github.com/sagikazarmark/danglib"
packages = ["path", "strings"]
```

```toml
# After
[imports.Path]
path = "./lib/path"
source = "github.com/sagikazarmark/danglib"
package = "path"

[imports.Strings]
path = "./lib/strings"
source = "github.com/sagikazarmark/danglib"
package = "strings"
```

Add the corresponding `import` declaration to every `.dang` file that uses the package. The next generation removes `vendor.gen.dang` only when it carries dangpkg's generated-file notice.

## Module Selection

To skip Dang modules during generation, configure `excludePaths` on the module constructor:

```dang
Dagger
  .dangpkg(excludePaths: [".dagger/modules/experimental"])
  .generateWorkspace(source)
```

## License

The project is licensed under the [MIT License](LICENSE).

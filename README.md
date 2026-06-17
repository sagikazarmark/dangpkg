# dangpkg

Package vendoring for Dang modules.

`dangpkg` reads `[[dependencies]]` from each `dang.toml`, fetches each dependency source from a git repository, and generates `vendor.gen.dang` for every Dang module that has dependencies.

## Usage

Install `dangpkg` in your Dagger module:

```bash
dagger install github.com/sagikazarmark/dangpkg
```

Create `dang.toml` in a Dang module directory:

```bash
dang toml
```

Add the packages you want to vendor. For example, to vendor packages from `danglib`:

```toml
[[dependencies]]
source = "https://github.com/sagikazarmark/danglib"
packages = ["path", "strings"]
```

Generate `vendor.gen.dang`:

```bash
dagger generate
```

## Dependency Sources

Each dependency `source` is a git repository, not a Dagger module source reference. Local paths are also supported for workspace-relative dependencies.

Packages are read from directories under the dependency source repository:

```text
path/*.dang
strings/*.dang
```

`*.test.dang` files are excluded from generated output.

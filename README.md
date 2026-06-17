# dangpkg

Package vendoring for Dang modules.

`dangpkg` reads `[[dependencies]]` from each `dang.toml`, lets Dagger resolve each dependency source, and generates `vendor.gen.dang` for every Dang module that has dependencies.

## Usage

Create `dang.toml` in a Dang module directory:

```toml
[[dependencies]]
source = "github.com/sagikazarmark/danglib"
packages = ["path", "strings"]
```

Generate `vendor.gen.dang`:

```bash
dagger call -m . generate
```

From another repository, replace `.` with the `dangpkg` module reference you use.

## Dependency Sources

Each dependency `source` is a Dagger module source reference. Dagger resolves each unique source once per generation pass.

Packages are read from directories under the dependency module source:

```text
path/*.dang
strings/*.dang
```

`*.test.dang` files are excluded from generated output.

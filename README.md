# arb-registry

The community package index for [arb](https://github.com/MenkeTechnologies/arb) —
a git-index registry (the crates.io-index / homebrew-core model). No server, no
API: the index is this repo's `index.json`, and packages are ordinary git repos.

## Index format

`index.json` maps a package name to where it lives and what it is:

```json
{
  "mypkg": { "repo": "https://github.com/you/mypkg", "version": "0.2.0", "desc": "a demo dashboard" }
}
```

## Client commands

```
arb update            # clone/fast-forward this index into ~/.arb/registry
arb search QUERY      # grep the index by name + description
arb install NAME      # git clone the package repo, validate it, install to ~/.arb/pkg
arb uninstall NAME
arb publish [GIT_URL] # validate the package here, upsert its entry, commit + push
```

`arb publish` fast-forward-pulls this index, adds/updates the package's entry,
commits, and pushes. With write access it lands directly; otherwise fork this
repo and open a PR with the committed change.

## Publishing a package

1. A package is a git repo with an `arb.toml` manifest and one or more exported
   `.arb` modules (`NAME.arb` / `main.arb`, or `[exports] modules = [...]`).
2. From the package directory: `arb publish` (uses the repo's `origin` remote),
   or `arb publish https://github.com/you/mypkg`.

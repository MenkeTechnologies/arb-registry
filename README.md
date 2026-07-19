```
 █████╗ ██████╗ ██████╗
██╔══██╗██╔══██╗██╔══██╗
███████║██████╔╝██████╔╝
██╔══██║██╔══██╗██╔══██╗
██║  ██║██║  ██║██████╔╝
╚═╝  ╚═╝╚═╝  ╚═╝╚═════╝
██████╗ ███████╗ ██████╗ ██╗███████╗████████╗██████╗ ██╗   ██╗
██╔══██╗██╔════╝██╔════╝ ██║██╔════╝╚══██╔══╝██╔══██╗╚██╗ ██╔╝
██████╔╝█████╗  ██║  ███╗██║███████╗   ██║   ██████╔╝ ╚████╔╝
██╔══██╗██╔══╝  ██║   ██║██║╚════██║   ██║   ██╔══██╗  ╚██╔╝
██║  ██║███████╗╚██████╔╝██║███████║   ██║   ██║  ██║   ██║
╚═╝  ╚═╝╚══════╝ ╚═════╝ ╚═╝╚══════╝   ╚═╝   ╚═╝  ╚═╝   ╚═╝
```

[![Docs](https://img.shields.io/badge/docs-online-blue.svg)](https://menketechnologies.github.io/arb/)
![license](https://img.shields.io/badge/license-MIT-ff2a6d?style=flat-square)
![model](https://img.shields.io/badge/model-git--index-05d9e8?style=flat-square)

### `[THE COMMUNITY PACKAGE INDEX FOR ARB]`

> *"No server, no API. The index is a JSON file in a git repo, and every package is an ordinary git repo."*

**arb-registry** is the community package index for
[`arb`](https://github.com/MenkeTechnologies/arb) — a **git-index registry** on
the crates.io-index / homebrew-core model. There is no service to run and no API
to call: the index is this repo's [`index.json`](index.json), packages are
ordinary git repos, and the `arb` CLI is the client. Resolution is a `git pull`;
publishing is a commit.

### [`Read the Docs`](https://menketechnologies.github.io/arb/) &middot; [`arb`](https://github.com/MenkeTechnologies/arb) &middot; [`index.json`](index.json)

---

## Table of Contents

- [\[0x00\] Overview](#0x00-overview)
- [\[0x01\] Index Format](#0x01-index-format)
- [\[0x02\] Client Commands](#0x02-client-commands)
- [\[0x03\] Publishing a Package](#0x03-publishing-a-package)
- [\[0x04\] Resolution](#0x04-resolution)
- [\[0x05\] Status](#0x05-status)
- [\[0xFF\] License](#0xff-license)

---

## [0x00] OVERVIEW

A registry is usually a server: an HTTP API, a database, an auth layer, uptime.
arb-registry is none of that. It borrows the model Cargo and Homebrew use for
their indexes — the catalog is a versioned file in a git repo — and applies it to
`arb` packages. The client (`arb` itself) clones this repo into
`~/.arb/registry`, reads `index.json`, and installs packages by cloning their
repos into `~/.arb/pkg`. The whole system is auditable in one file and works
offline once pulled.

## [0x01] INDEX FORMAT

`index.json` maps a package name to where it lives and what it is:

```json
{
  "mypkg": { "repo": "https://github.com/you/mypkg", "version": "0.2.0", "desc": "a demo dashboard" }
}
```

## [0x02] CLIENT COMMANDS

```sh
arb update             # clone/fast-forward this index into ~/.arb/registry
arb search QUERY       # grep the index by name + description
arb install NAME       # git clone the package repo, validate it, install to ~/.arb/pkg
arb uninstall NAME
arb publish [GIT_URL]  # validate the package here, upsert its entry, commit + push
```

`arb publish` fast-forward-pulls this index, adds/updates the package's entry,
commits, and pushes. With write access it lands directly; otherwise fork this
repo and open a PR with the committed change.

## [0x03] PUBLISHING A PACKAGE

1. A package is a git repo with an `arb.toml` manifest and one or more exported
   `.arb` modules (`NAME.arb` / `main.arb`, or `[exports] modules = [...]`).
2. From the package directory: `arb publish` (uses the repo's `origin` remote),
   or `arb publish https://github.com/you/mypkg`.

## [0x04] RESOLUTION

`import NAME` in a spec resolves in order: **local** file → `~/.arb/lib/NAME` →
installed package (`~/.arb/pkg/NAME`) → stdlib preset. `arb install NAME` reads
this index for the repo URL, clones and validates the package, and drops it under
`~/.arb/pkg` so the `import` resolves.

## [0x05] STATUS

Active. The index ships today (`git` subprocess as transport — no in-process
TLS), with semver checking against the index version, a visited-set cycle guard,
and the `~/.arb/pkg` resolver tier. `arb publish` is client-only pending the
hosted index.

## [0xFF] LICENSE

MIT.

# aur-packages

Packaging for shbernal's AUR packages: each one is maintained in this repo and published to its own AUR repo.

## Language

**Package**:
One dir under `packages/` holding a PKGBUILD, published to exactly one AUR repo.
_Avoid_: pkgbase, package dir

**Built package**:
A `.pkg.tar.zst` that makepkg produces from a Package.
_Avoid_: package (alone), package file, artifact

### Upstream

**Upstream**:
The project whose software a Package ships, such as `owasp-amass/amass`.
_Avoid_: source (that's the PKGBUILD's `source=` array)

**Upstream release**:
A tagged version of an Upstream, as nvchecker reports it.
_Avoid_: tag, new version

**Versioned package**:
A Package pinned to an Upstream release and kept current by the Updates workflow. It has an `.nvchecker.toml`.

**VCS package**:
A `-git` Package that builds the Upstream's latest commit and works out its own `pkgver` at build time. The Updates workflow ignores it.
_Avoid_: unversioned package

**Prebuilt package**:
A `-bin` Package that ships the Upstream's prebuilt binaries instead of building from source.
_Avoid_: binary package

### Lifecycle

**Check**:
Building one Package in `archlinux:base-devel` and running namcap on its PKGBUILD and Built packages. What CI runs per changed Package.
_Avoid_: test, lint

**namcap exclusion**:
A namcap rule skipped for one Package because the error is expected for it.
_Avoid_: skip, ignore

**Bump**:
A change to a Package's version in its PKGBUILD: a new `pkgver` for an Upstream release, with `pkgrel` reset, or a `pkgrel` increment for a packaging fix.
_Avoid_: upgrade, update

**Update PR**:
A pull request opened by the Updates workflow for one Upstream release, carrying the Bump of every Package built from that Upstream.

**AUR repo**:
The git repo on aur.archlinux.org that one Package is published to. A deploy target: only Publish writes to it.
_Avoid_: AUR remote, AUR package (for the repo)

**Publish**:
Making a Package's AUR repo match this repo: its tracked files, minus `.nvchecker.toml`, plus a generated `.SRCINFO`.
_Avoid_: deploy, push, release, sync

**In sync**:
An AUR repo whose tree already matches what Publish would write.

## Flagged ambiguities

- **package** meant the dir under `packages/`, the AUR package and the `.pkg.tar.zst`. Resolved: Package is the dir (one AUR repo), Built package is the file.
- **update / upgrade / bump** all named the version change. Resolved: Bump. "Update" survives only in the names Updates workflow and Update PR; "upgrade" is what users do with pacman.
- **monorepo / GitHub / this repo** all named the repo holding the Packages. Resolved: "this repo" (`aur-packages` in text that leaves it).

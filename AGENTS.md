# AI project guidelines

`aur-packages`: the PKGBUILDs of shbernal's AUR packages, one directory per package under `packages/`. GitHub is the source of truth; the AUR repos are deploy targets written only by `publish.yml`.

- Key commands
  - `docker run --rm -v "$PWD:/repo:ro" -w /repo archlinux:base-devel scripts/check-package <pkg>`: what CI runs per package
  - `scripts/publish-package --dry-run <pkg>`: diff between this repo and the AUR
  - `docker run --rm -e HOST_UID="$(id -u)" -e GITHUB_TOKEN -v "$PWD:/repo" -w /repo archlinux:base-devel scripts/upgrade-packages`: bump everything to upstream

- Conventions
  - `.SRCINFO` is never committed; `publish-package` generates it.
  - Every versioned package has an `.nvchecker.toml` (`-git` packages don't). It is not published to the AUR.
  - namcap errors fail CI. An error expected for one package goes in `.github/namcap-exclude`, not a blanket skip.
  - Adding a package: create the dir with a PKGBUILD (and `.nvchecker.toml`), the AUR repo is created on first publish. Removing a dir doesn't touch the AUR; deletion is a manual AUR request.

- Secrets
  - `aur` environment (main only, required reviewer): `AUR_SSH_PRIVATE_KEY` (dedicated CI key, not the personal one), `AUR_KNOWN_HOSTS`.
  - `UPDATES_TOKEN`: fine-grained PAT for this repo (contents, pull requests: write), so update PRs trigger CI.

- Iron Laws
  - State of the art AI models/harnesses need minimal guidance. Don't repeat yourself, don't babysit, don't be over-specific.
  - Only document items here when model diverges repeatedly from expected behavior. Don't document behaviors implemented without errors.
  - No journaling: code comments / documentation describe current state, they don't carry a log of their own edit history.

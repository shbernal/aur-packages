# Contributing

## Submitting a new package

AUR registration is closed for now. If you can't upload a package yourself, submit your PKGBUILD here as a pull request. Once it merges, I publish it to the AUR under my account and maintain it from this repo. I take packages I can reasonably vouch for and maintain, and may decline others.

This is a stopgap. Once registration reopens, upstream can take the package over. Open an issue and I'll make their account the maintainer of the AUR package and remove it from this repo.

Before you open the pull request:

1. Check that the package isn't in the official repos or already on the AUR, and that it follows the [AUR submission guidelines](https://wiki.archlinux.org/title/AUR_submission_guidelines).
2. Add a directory `packages/<pkgname>/` holding:
   - `PKGBUILD`.
   - `.nvchecker.toml`, unless it's a `-git` package. The Updates workflow reads it to open a pull request for each upstream release. See [`packages/remder-bin/.nvchecker.toml`](packages/remder-bin/.nvchecker.toml) and the [nvchecker docs](https://nvchecker.readthedocs.io/en/latest/usage.html).
   - Any files the PKGBUILD installs (`.desktop`, `.install`, patches).
   - For signed sources, each key in `validpgpkeys` as `keys/pgp/<fingerprint>.asc` (`gpg --export --armor <fingerprint>`). CI imports these to verify the signatures.

   Don't add `.SRCINFO`. Publishing generates it.
3. Start the PKGBUILD with this header, your name going on the `Contributor` line:

   ```bash
   # Maintainer: shbernal <shbernal.01@gmail.com>
   # Contributor: Your Name <you at example dot com>
   # Issues and fixes: https://github.com/shbernal/aur-packages
   ```

4. Use real checksums (`SKIP` only for VCS sources) and an SPDX identifier in `license`. Name packages that ship upstream binaries `-bin` and packages that build the latest commit `-git`, with `provides` and `conflicts` on the plain name.
5. Pin every source to a release or commit. A URL that follows a branch (`raw/main/...`) or the latest release changes under its checksum.
6. Run the same check CI runs. It checks the conventions above, builds the package in a clean container and runs namcap on the PKGBUILD and the built package:

   ```bash
   docker run --rm -v "$PWD:/repo:ro" -w /repo archlinux:base-devel scripts/check-package <pkgname>
   ```

   namcap errors fail CI, and so does a package built from source whose binaries lack FULL RELRO or PIE, a sign the build ignores makepkg's `CFLAGS`/`LDFLAGS`. If an error is expected for your package (an Electron app under `/opt`, say), add a line to [`.github/namcap-exclude`](.github/namcap-exclude) with a comment saying why.

In the pull request, link the upstream project and fill in the AI disclosure line. Keep commit messages free of AI attribution such as `Co-Authored-By` trailers; the disclosure belongs in the description.

## Changing an existing package

Same flow. For a packaging fix that leaves `pkgver` alone, increment `pkgrel`. Version bumps for versioned packages come from the Updates workflow, so you don't need to send those.

## Reporting a problem

Open an issue here for packaging problems. Bugs in the software itself belong upstream.

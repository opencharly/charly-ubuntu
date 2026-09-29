# charly-ubuntu

The Ubuntu package repository for [charly](https://github.com/opencharly/charly) — the OpenCharly CLI and its composed toolchain, packaged as `.deb` for `amd64` and `arm64`.

This repo owns the artifact **and the R10 bed that proves it**: the
`check-ubuntu-repo` deploy boots a disposable Ubuntu 24.04 (noble) cloud VM, adds
the published charly apt repo, installs the packaged `charly`, and asserts the
installed binary's version equals the version the package manager recorded.

## Add the repository

```sh
curl -fsSL https://opencharly.github.io/charly-ubuntu/charly.gpg | gpg --dearmor -o /etc/apt/keyrings/charly.gpg
echo "deb [signed-by=/etc/apt/keyrings/charly.gpg] https://opencharly.github.io/charly-ubuntu/ stable main" > /etc/apt/sources.list.d/charly.list
apt update
apt install charly
```

## Direct install

Download the `.deb` for your architecture and install it with `apt install`:

- amd64: `https://opencharly.github.io/charly-ubuntu/pool/main/c/charly/charly-amd64.deb`
- arm64: `https://opencharly.github.io/charly-ubuntu/pool/main/c/charly/charly-arm64.deb`

## Variants

| Package | Plugin set |
|---|---|
| `charly` | secrets, feature, vm, doctor, clean, settings, candy, mcp, review, pipeline (10) |
| `charly-full` | the default set + udev, preempt (12) |
| `charly-minimal` | doctor, clean, settings (3) |

## Triggering a build

The build workflow is manual: **Actions → build → Run workflow**, entering the
charly release CalVer to package (e.g. `2026.227.1026`). The main repo's release
is the source of truth for the binary, the plugins, and the packaging metadata.
Each build assembles the repo for both `amd64` and `arm64`, signs the `.deb`
files and the `Release` metadata, and install-tests the result before deploying
to GitHub Pages.

## Verification

- **CI install-test** (inside the build workflow): installs `charly` from a
  local `file://` mount of the assembled repo with `signed-by` key
  verification, asserts `charly version` equals the packaged release, asserts
  the default-variant `plugin-<word>` set is served by the shared `charly-lib`
  host, and runs `charly doctor` from a non-project directory.
- **R10 bed** `check-ubuntu-repo`: `charly check run check-ubuntu-repo` boots
  the disposable Ubuntu 24.04 VM (UEFI), installs the packaged `charly` from the
  PUBLISHED repo, and asserts the version match plus project-less plugin
  dispatch.

## Layout

- `charly.yml` — the `ubuntu-repo-vm` `kind: vm` template and the
  `check-ubuntu-repo` bed.
- `.github/workflows/build.yml` — the manual package build + Pages deploy.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `charly.gpg` — the apt repo signing key.
- `index.html` — the Pages landing page.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-tools:charly` — the charly binary and its per-distro package repos.
- Ubuntu distro: `/charly-distros:ubuntu` — the Ubuntu base box and vocabulary.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder.
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella.

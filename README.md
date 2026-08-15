# charly-ubuntu

The Ubuntu package repository for [charly](https://github.com/opencharly/charly) — the OpenCharly CLI and its composed toolchain, packaged as `.deb` for `amd64` and `arm64`.

The repository is built by a GitHub Actions workflow (manual dispatch with a charly release CalVer) and published to GitHub Pages. Each build produces the `charly` package plus the named variants `charly-full` and `charly-minimal` (differing in the baked-in plugin set), signs the `.deb` files and the `Release` metadata, and install-tests the result before deploying.

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
| `charly` | secrets, feature, vm, doctor, clean, settings, candy |
| `charly-full` | the default set + udev, preempt |
| `charly-minimal` | doctor, clean, settings |

## Triggering a build

The workflow is manual: **Actions → build → Run workflow**, entering the charly release CalVer to package (e.g. `2026.227.1026`). The main repo's release is the source of truth for the binary, the plugins, and the packaging metadata.

## Verification

Each build install-tests the packages from a local `file://` mount of the assembled repository before deploying: it installs `charly` with `signed-by` key verification, asserts `charly version` equals the packaged release, and runs `charly doctor` from a non-project directory to prove the baked plugins dispatch project-less.

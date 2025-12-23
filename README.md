# EasyTier Arch Package (GitHub Actions)

This repository builds EasyTier into an Arch Linux package (`.pkg.tar.zst`) using GitHub Actions.

## What it builds

- `easytier-core`
- `easytier-cli`
- `easytier-web`

Source code is fetched from: https://github.com/EasyTier/EasyTier

## Local build (on Arch)

```bash
sudo pacman -Syu --needed base-devel rust cargo protobuf git clang llvm
cd packaging/arch
makepkg -s
```

The package file will be created in `packaging/arch/`.

## CI build (GitHub Actions)

- Workflow: `.github/workflows/build-archpkg.yml`
- Uses `archlinux:base-devel` container
- Installs `rust cargo protobuf git clang llvm` and runs `makepkg`
- Build outputs are uploaded as GitHub Actions artifacts

## Notes

- `protobuf` is required because EasyTier's build uses `protoc` on Linux.
- `clang/llvm` are required because `kcp-sys` uses `bindgen`, which needs `libclang`.
- `sha256sums` is set to `SKIP` by default for convenience; for real distribution you should pin checksums.

## Bumping EasyTier version

Edit `packaging/arch/PKGBUILD`:

- `pkgver=...`
- `source=...v$pkgver.tar.gz`

Optionally compute checksums with `updpkgsums` (from `pacman-contrib`).

## Create a public GitHub repository

1. Create an empty *public* repository on GitHub (UI).
2. Push this folder to GitHub:

```bash
git init
git add -A
git commit -m "Initial GitHub Actions Arch package builder"
git branch -M main
git remote add origin <your_github_repo_url>
git push -u origin main
```

After that, open the Actions tab and download the artifact `*.pkg.tar.zst`.
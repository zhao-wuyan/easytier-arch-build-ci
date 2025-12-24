# EasyTier Arch Package (GitHub Actions)

This repository builds EasyTier into Arch Linux packages (`.pkg.tar.zst`) using GitHub Actions.

## What it builds

- `easytier` package
  - `easytier-core`
  - `easytier-cli`
  - `easytier-web`
- `easytier-gui` package
  - `easytier-gui` (Tauri desktop app)

Source code is fetched from: https://github.com/EasyTier/EasyTier

## Local build (on Arch)

```bash
sudo pacman -Syu --needed base-devel \
  rust cargo protobuf git clang llvm pkgconf zstd \
  nodejs pnpm python \
  webkit2gtk gtk3 librsvg libayatana-appindicator

cd packaging/arch
makepkg -s
```

The package files will be created in `packaging/arch/`.

## CI build (GitHub Actions)

- Workflow: `.github/workflows/build-archpkg.yml`
- Uses `archlinux:base-devel` container
- Runs `makepkg` and uploads artifacts

## SteamOS / Steam Deck (single file)

If you want a single file that can be downloaded and run on SteamOS, use the AppImage build:

- Workflow: `.github/workflows/build-appimage.yml`
- Output: `*.AppImage` artifact

On SteamOS (Desktop Mode):

1. Download the `*.AppImage`
2. Make it executable: `chmod +x easytier-gui*.AppImage`
3. Run it: `./easytier-gui*.AppImage`

## Notes

- `protobuf` is required because EasyTier's build uses `protoc` on Linux.
- `clang/llvm` are required because `kcp-sys` uses `bindgen`, which needs `libclang`.
- `pkgconf` + `zstd` are required so `zstd-sys` can link to `libzstd`.
- `nodejs/pnpm/python` and `webkit2gtk/gtk3/...` are required to build the Tauri GUI.
- If you see linker errors about `ring_*` or `ikcp_*`, they may be caused by GCC LTO objects being linked with `lld`; this repo strips `-flto=auto` / `-fuse-ld=lld` during the build inside `PKGBUILD`.

## Bumping EasyTier version

Edit `packaging/arch/PKGBUILD`:

- `pkgver=...`
- `source=...v$pkgver.tar.gz`

Optionally compute checksums with `updpkgsums` (from `pacman-contrib`).

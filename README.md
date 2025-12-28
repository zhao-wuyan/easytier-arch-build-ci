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
3. Run it:
   - Normal: `./easytier-gui*.AppImage`
   - If you see `Cannot mount AppImage, please check your FUSE setup`: `./easytier-gui*.AppImage --appimage-extract-and-run`

Notes:

- SteamOS may not ship `fuse2` by default. Installing `fuse2` enables normal (mount-based) AppImage execution.
- If you see `GLIBC_2.42 not found` on SteamOS (glibc 2.41), rebuild the AppImage using an older Arch Linux Archive snapshot:
  - `workflow_dispatch` input `arch_snapshot` (default: `2025/07/01`)

## Flatpak (SteamOS recommended)

SteamOS may have issues running AppImage (FUSE/glibc/Wayland/WebKitGTK). Flatpak is usually the most reliable option.

- Manifest: `packaging/flatpak/io.github.easytier.EasyTierGUI.yml`
- CI workflow (test build): `.github/workflows/build-flatpak.yml`

Flathub note: Flathub builds are offline. This repo’s CI pre-generates a `pnpm` store tarball and uses `pnpm install --offline`; for Flathub you should pin that tarball as a remote source (or generate `generated-sources*.json`).

## Notes

- `protobuf` is required because EasyTier's build uses `protoc` on Linux.
- `clang/llvm` are required because `kcp-sys` uses `bindgen`, which needs `libclang`.
- `pkgconf` + `zstd` are required so `zstd-sys` can link to `libzstd`.
- `nodejs/pnpm/python` and `webkit2gtk/gtk3/...` are required to build the Tauri GUI.
- CI builds strip `vite-plugin-vue-devtools` from `easytier-gui/vite.config.ts` because it may crash in Node/CI (browser `localStorage` assumption), and also inject a minimal `localStorage` polyfill via `NODE_OPTIONS=--import ...` for other Node-side tooling.
- If you see linker errors about `ring_*` or `ikcp_*`, they may be caused by GCC LTO objects being linked with `lld`; this repo strips `-flto=auto` / `-fuse-ld=lld` during the build inside `PKGBUILD`.

## Bumping EasyTier version

Edit `packaging/arch/PKGBUILD`:

- `pkgver=...`
- `source=...v$pkgver.tar.gz`

Optionally compute checksums with `updpkgsums` (from `pacman-contrib`).

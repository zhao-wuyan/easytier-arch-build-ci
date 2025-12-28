# Flatpak packaging (Flathub-ready checklist)

This repo includes a Flatpak manifest for EasyTier GUI:

- `packaging/flatpak/io.github.easytier.EasyTierGUI.yml`

It is primarily meant to support SteamOS/Steam Deck users where AppImage can be problematic (FUSE/glibc).

## CI test build

- Workflow: `.github/workflows/build-flatpak.yml`
- Produces a `*.flatpak` bundle artifact for quick testing.

## Flathub submission notes (important)

Flathub builds are offline (no network during build), so **Node dependencies must be vendored**.

This repo uses a pragmatic approach for CI:

- CI pre-generates a `pnpm` store tarball (`packaging/flatpak/pnpm-store.tar.gz`)
- The Flatpak manifest includes it as a local source and runs `pnpm install --offline`

For Flathub submission, you should replace the local `path: pnpm-store.tar.gz` source with either:

1. A pinned remote URL + sha256 (e.g. a GitHub release asset you publish), or
2. The more “standard” Flathub approach: use `flatpak-builder-tools` node generator to produce `generated-sources*.json`.

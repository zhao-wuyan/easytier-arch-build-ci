# Flatpak packaging (Flathub-ready checklist)

This repo includes a Flatpak manifest for EasyTier GUI:

- `packaging/flatpak/io.github.easytier.EasyTierGUI.yml`

It is primarily meant to support SteamOS/Steam Deck users where AppImage can be problematic (FUSE/glibc).

## CI test build

- Workflow: `.github/workflows/build-flatpak.yml`
- Produces a `*.flatpak` bundle artifact for quick testing.

## Flathub submission notes (important)

Flathub builds are offline (no network during build), so **Node dependencies must be vendored**.

Typical approach:

1. Use `flatpak-builder-tools` (node generator) to generate sources from the lockfile.
2. Add the generated `generated-sources*.json` as a `type: file` source in the manifest.
3. Set `build-options: { no-network: true }` for the module before submitting to Flathub.

This repo does not yet include the generated Node dependency sources.

# CLAUDE.md — vardot/drupal-core-patches

Working notes.

- **Purpose**: hold Drupal **core** patches for Varbase, one git branch per Drupal core MAJOR.MINOR.
- **Type**: Composer `metapackage` (no code). Patches declared in `extra.patches."drupal/core"`.
- **Branches**: `11.3.x`, `10.6.x` (core-minor); `patches` (flat `.patch` file store, no composer.json).
- **Binding**: each core-minor branch sets `conflict: {"drupal/core": "<minor.0 || >=next.0"}` so Composer
  selects the release matching the installed core. Consumers require `~10 || ~11 || ~12` (or `~11 || ~12`).
- **Patch files**: stored on the `patches` branch; referenced by raw URL
  `https://raw.githubusercontent.com/Vardot/drupal-core-patches/refs/heads/patches/<file>`.
- **Consumed by**: `vardot/varbase-patches` (allowlists this package so its patches apply).
- **Releasing**: tag semver within the minor; **never move a tag** (Packagist fails). See docs/releasing.md.
- Commit author: Rajab Natshah <rajabn@gmail.com>. Disclose AI use.

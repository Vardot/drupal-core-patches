# Drupal core patches

[![Test patches (11.4.x)](https://github.com/Vardot/drupal-core-patches/actions/workflows/test-patches.yml/badge.svg?branch=11.4.x)](https://github.com/Vardot/drupal-core-patches/actions/workflows/test-patches.yml?query=branch%3A11.4.x)

> **Why this package:** `vardot/drupal-core-patches` is required by [`vardot/varbase-patches`](https://github.com/Vardot/varbase-patches) so that [Varbase](https://www.drupal.org/project/varbase) can upgrade to the latest Drupal core versions. It maintains the right set of working Drupal **core** patches **per Drupal core version** (one branch per major.minor), so each Varbase line automatically gets the patches that apply to its Drupal core.

Curated **Drupal core** patches used by [Varbase](https://www.drupal.org/project/varbase),
delivered as a Composer **metapackage** with **one git branch per Drupal core MAJOR.MINOR**
version (e.g. `11.3.x`, `10.6.x`). Consumed by
[`vardot/varbase-patches`](https://github.com/Vardot/varbase-patches) so that core patches are
versioned and resolved independently of the Varbase line.

- Patches are declared under `extra.patches."drupal/core"` and applied by
  [`cweagans/composer-patches`](https://github.com/cweagans/composer-patches) via the
  Varbase Patches plugin (which allowlists this package).
- Each core-minor branch carries **only** the patches valid for that exact Drupal core minor and
  uses `conflict: { "drupal/core": "<minor.0 || >=next.0" }` to bind itself to that minor, so
  Composer always selects the release matching the installed core.
- The actual `.patch` files live on the **`patches`** branch and are referenced by raw URL
  (the same model `varbase-patches` uses for its patch files).

## Usage

```bash
composer require vardot/drupal-core-patches:~11.3.0
```

`varbase-patches` does this for you. To allow Composer to pick the release matching the site's core,
it requires a range that spans the supported majors:

| varbase-patches branch | Drupal core | requires |
|---|---|---|
| 9.1.x, 9.2.x, 10.0.x | ~10.6 | `vardot/drupal-core-patches: ~10 \|\| ~11 \|\| ~12` |
| 10.1.x, 11.0.x | ~11.3 | `vardot/drupal-core-patches: ~11 \|\| ~12` |

## Branch per Drupal core major.minor

| Drupal core | Branch    | Patch count |
|-------------|-----------|-------------|
| ~11.3       | `11.3.x`  | 19 |
| ~10.6       | `10.6.x`  | 17 |
| (files)     | `patches` | shared `.patch` file store |

Add a new branch (e.g. `11.4.x`) when supporting a new Drupal core minor — see
[docs/adding-a-core-version.md](docs/adding-a-core-version.md).

## Documentation

- [Architecture](docs/architecture.md)
- [Usage & installation](docs/usage.md)
- [Adding a Drupal core version](docs/adding-a-core-version.md)
- [Releasing](docs/releasing.md)
- [Troubleshooting](docs/troubleshooting.md)

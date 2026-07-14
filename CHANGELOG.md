# Changelog — Vardot/drupal-core-patches (`10.5.x`)

All notable changes on the `10.5.x` branch of [`Vardot/drupal-core-patches`](https://github.com/Vardot/drupal-core-patches), newest first.
Each release lists the commits — merged pull requests and the drupal.org issues they reference — since the previous release.
`#N` links to the pull request; 7-digit `#NNNNNNN` refs are drupal.org issues. Generated from git history.

## [Unreleased]

- fix: Remove `Issue #3044656: Add a helper method to strip subdirectories from URL paths` -- unused (Varbase moved to the new routing system) and upstream #3044656 is Needs work (`UrlHelper` is a `Drupal\Component` class and must not depend on the request stack); `3044656-2.patch` stays on the `patches` branch untouched for already-released tags

## [10.5.0.2] - 2026-07-06

- docs: Add `CHANGELOG.md` for the `10.5.x` branch (#5)

## [10.5.0.1] - 2026-06-28

- task: keep it a pure Drupal core patches storage metapackage, not a Composer plugin

## [10.5.0] - 2026-06-28

- Initial tracked release on the `10.5.x` branch.


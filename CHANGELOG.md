# Changelog — Vardot/drupal-core-patches (`10.6.x`)

All notable changes on the `10.6.x` branch of [`Vardot/drupal-core-patches`](https://github.com/Vardot/drupal-core-patches), newest first.
Each release lists the commits — merged pull requests and the drupal.org issues they reference — since the previous release.
`#N` links to the pull request; 7-digit `#NNNNNNN` refs are drupal.org issues. Generated from git history.

## [Unreleased]

- ci: Fix the "Upload the install log" artifact name on PR runs -- `github.ref_name` resolves to `<PR>/merge` on a pull_request run, and the `/` made `actions/upload-artifact@v4` reject the name and fail the job `if: always()` even when every patch applied cleanly (that is what made #58-#61 report red). PR runs now use `pr-<number>`; branch pushes keep the readable `<branch>` name

## [10.6.0.4] - 2026-07-06

- docs: Add `CHANGELOG.md` for the `10.6.x` branch (#6)

## [10.6.0.3] - 2026-06-28

- task: keep it a pure Drupal core patches storage metapackage, not a Composer plugin

## [10.6.0.2] - 2026-06-28

- task: require drupal/core ~10.6.0 instead of a conflict range
- docs: drop license badge; explain use in varbase-patches and per-Drupal-version patch switch
- docs: PR/MR template with Checkpoints (no UX/UI line)
- docs: add PR/MR template with Checkpoints

## [10.6.0.1] - 2026-06-28

- docs: comprehensive docs, LICENSE, and varbase-patches composer style (10.6)
- docs: add GPL-2.0-or-later LICENSE

## [10.6.0] - 2026-06-28

- Initial tracked release on the `10.6.x` branch.


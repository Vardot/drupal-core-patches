# Changelog — Vardot/drupal-core-patches (`11.3.x`)

All notable changes on the `11.3.x` branch of [`Vardot/drupal-core-patches`](https://github.com/Vardot/drupal-core-patches), newest first.
Each release lists the commits — merged pull requests and the drupal.org issues they reference — since the previous release.
`#N` links to the pull request; 7-digit `#NNNNNNN` refs are drupal.org issues. Generated from git history.

## [Unreleased]

- ci: Add a GitHub Actions patches test — installs the Drupal core this branch targets and checks that Composer Patches (v1 and v2) applies every core patch, and that every patch file still exists

- docs: lock the patch issue/PR title standard in the agent docs (#29)

## [11.3.0.5] - 2026-07-06

- docs: Add `CHANGELOG.md` for the `11.3.x` branch (#9)

## [11.3.0.4] - 2026-06-28

- task: keep it a pure Drupal core patches storage metapackage, not a Composer plugin

## [11.3.0.3] - 2026-06-28

- task: patch drupal/core for #3606822 (ClassResolver synthetic kernel on install)
- revert: drop #3564735 core patch (already in core 11.3.13)
- task: patch drupal/core for #3564735 (synthetic kernel on CLI install)
- docs: document the smart core-patching workflow in AGENTS.md/CLAUDE.md

## [11.3.0.2] - 2026-06-28

- task: require drupal/core ~11.3.0 instead of a conflict range
- docs: drop license badge; explain use in varbase-patches and per-Drupal-version patch switch
- docs: PR/MR template with Checkpoints (no UX/UI line)
- docs: add PR/MR template with Checkpoints

## [11.3.0.1] - 2026-06-28

- docs: add GPL-2.0-or-later LICENSE
- docs: comprehensive documentation and logic for drupal-core-patches (#2)

## [11.3.0] - 2026-06-28

- Initial tracked release on the `11.3.x` branch.


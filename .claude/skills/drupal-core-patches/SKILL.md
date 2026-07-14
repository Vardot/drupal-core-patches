---
name: drupal-core-patches
description: Maintain vardot/drupal-core-patches — the Composer metapackage holding Varbase's curated Drupal core patches, with one git branch per Drupal core major.minor (10.4.x … 12.0.x, plus a flat "patches" file-store branch). Covers the branch-per-core-minor scheme, building a core-minor set from varbase-patches history, immutable dated patch files, Packagist-safe 4-segment never-move release tags, and wiring it into vardot/varbase-patches (require range + allowlist). Use when adding a Drupal core minor branch, re-rolling/curating a core patch set, releasing the package, or debugging why a core patch is not applied.
---

# Drupal Core Patches (vardot/drupal-core-patches)

A Composer **metapackage** that isolates Varbase's curated **Drupal core** patches from the Varbase line, so Varbase can upgrade to the latest Drupal core while keeping the right patch set per core version. It is required by `vardot/varbase-patches`. It is **NOT a Composer plugin** — never list it in `config.allow-plugins`; only in `extra.composer-patches.allowed-dependency-patches`.

## Branch per Drupal core major.minor

| Branch    | Drupal core | Notes                                   |
|-----------|-------------|-----------------------------------------|
| `12.0.x`  | `~12.0.0`   | forward-compat placeholder (no patches) |
| `11.4.x`  | `~11.4.0`   | forward-compat placeholder (no patches) |
| `11.3.x`  | `~11.3.0`   | default branch                          |
| `11.2.x` / `11.1.x` | `~11.2.0` / `~11.1.0` |                          |
| `10.6.x` / `10.5.x` / `10.4.x` | `~10.6.0` … | per core minor          |
| `patches` | n/a         | flat `.patch` file store (read-only)    |

Each core-minor branch:

```json
{
  "require": { "drupal/core": "~<minor>.0", "cweagans/composer-patches": "~1.7.0 || ~2.0" },
  "extra": { "patches": { "drupal/core": { "<description>": "<raw patches-branch URL>" } } }
}
```

The `require drupal/core ~<minor>.0` binds the release to that core minor, so Composer only picks the release whose minor matches the installed core. Patch URLs point at the `patches` branch:
`https://raw.githubusercontent.com/Vardot/drupal-core-patches/refs/heads/patches/<file>`.

## How the right set is chosen

The consumer (`vardot/varbase-patches`) requires the broad range (`~10 || ~11 || ~12`). Because each drupal-core-patches release pins `drupal/core ~<minor>.0`, Composer resolves the one release matching the installed core → the site automatically gets the patch set for ITS Drupal core.

## Building a core-minor set from varbase-patches history

1. Group varbase-patches tags by their `drupal/core` constraint (`git show <tag>:composer.json` → `require.drupal/core` + `extra.patches."drupal/core"`).
2. For the target core minor, take the **latest** varbase-patches tag whose constraint includes `~<minor>.0` and use its `drupal/core` patch set.
3. Download those patch files into the `patches` branch; point the new branch's composer URLs at them.
4. Create `<minor>.x` off the nearest branch; set `require drupal/core ~<minor>.0` + the set (two-line format); copy docs/LICENSE/PR-template.
5. Tag `<minor>.0`.

## Immutable patch files — never re-roll in place

A `.patch` on the `patches` branch is immutable. To re-roll (new core minor, updated MR, corrected diff), add a **NEW** file with **today's** date — `drupal-core--$(date +%Y-%m-%d)--<issue>--mr-<N>.patch` — never the old date/filename, never overwrite; point the branch composer URLs at the new file and leave the old one so pinned releases keep resolving. Only exception: same-day content fix on a file created today, before any release referenced it.

Materialize every drupal.org core MR through `ddev composer var-ccup` (or an equivalent producing a static, timestamped, standard-named file) — never a raw MR URL (URLs drift, break checksums). Verify the diff starts with `diff --git`, not the git.drupalcode.org bot-challenge `<!DOCTYPE html>`; if HTML, `git diff origin/<target>...<mrBranch> > patches/<file>.patch`.

## Patching history (`CHANGELOG.md`) — add the entry with the patch

When you add a patch to the patch list (`composer.json` `extra.patches`) in `vardot/varbase-patches` or `vardot/drupal-core-patches` — or change / remove one — add a matching entry under that branch's `## [Unreleased]` section of `CHANGELOG.md` **in the same change**. Each release branch keeps one newest-first `CHANGELOG.md`; the Unreleased section stages what the next release regenerates. Never ship a patch change without its Unreleased line.

## Releasing (CRITICAL)

- **Release title = tag only.** A GitHub Release on `vardot/varbase-patches` or `vardot/drupal-core-patches` MUST use the **tag as its exact title/name** (e.g. `11.4.0.4`, `9.2.94`) — no description suffix, no "Varbase Patches …" / "Drupal core … patch set" text in the title. Any human-readable summary goes in the release **notes/body**, never the title.
- Tag semver **within the minor** — `11.3.0`, then `11.3.0.1`, `11.3.0.2`, … (4 segments for a re-release).
- **Never move a tag** — Packagist rejects moved tags ("The last update failed"). Re-release an already-tagged commit by cutting a NEW 4-segment tag; never `git tag -f`.
- Packagist needs the GitHub webhook (`https://packagist.org/api/github` + the maintainer's API token) or a manual **Update** click; the `patches` branch needs no composer/version.
- Future cores (`11.4.x`, `12.0.x`) stay forward-compat placeholders: `require drupal/core ~<minor>.0` with an **empty** `extra.patches."drupal/core"` until patches are re-rolled for that core.
- **Tick `Release` after the tag.** After you cut / publish a release tag for `vardot/varbase-patches` or `vardot/drupal-core-patches`, tick the `- [x] Release` checkpoint on the associated **issue AND PR**, adding a link to the released tag (e.g. `Released in https://github.com/Vardot/drupal-core-patches/releases/tag/11.3.0.5`). `Release` is a factual post-release tick done by the releaser — this is **allowed**; it does **not** let the AI tick `Reviewed by a human` or `Code review by maintainers` (human-only).

## Wiring into vardot/varbase-patches

`vardot/varbase-patches` **requires** `vardot/drupal-core-patches` (`~10 || ~11 || ~12` on 9.1.x/9.2.x/10.0.x; `~11 || ~12` on 10.1.x/11.0.x) and its plugin allowlists the package so the core patches apply — via the constant `VarbasePatchesPlugin::DEFAULT_ALLOWED_DEPENDENCY_PATCHES` used by both the v1 `buildV1PatchesMap` and the v2 `FilteredDependencies` resolver.

## Issue / PR titles + shared-file rule

Same grammar as varbase-patches — `<Action> a patch for the <Target> on <ref>` (Add / Remove / Change / Update / Revert -) — Target is usually **`Drupal Core`**, the core minor is the context. Copy the upstream drupal.org issue's FULL title verbatim (no paraphrase, no `fix:`/`task:` prefix, no `(#id)` duplication). One materialized core `.patch` on the file-store branch, referenced from each core-minor branch that needs it — never duplicated. Every PR ends with the Checkpoints checklist. Read the branch `CHANGELOG.md` before adding/removing/changing a patch.

## Related skills & agents

- Paired agent: **drupal-core-patches** — the full sub-agent form of this skill.
- **varbase-patches** skill + agent — the plugin that requires this metapackage; contrib (non-core) patches live there.
- **drupal-patches** skill — base cweagans/composer-patches declare/apply/create/re-roll mechanics.
- **vardot-mr-pr-manager** skill + agent — opens/maintains the patch PRs; **vardot-issue-templates** skill for the issue + Checkpoints templates.

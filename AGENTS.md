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

## Smart Drupal-core patching workflow (varbase-patches + drupal-core-patches)

**Goal:** keep Varbase upgradable to the latest Drupal core by isolating the **Drupal core** patches
from the Varbase line, one set per Drupal core version.

### Packages
- **`vardot/drupal-core-patches`** — Composer `metapackage`, **one git branch per Drupal core
  MAJOR.MINOR** (`10.4.x`, `10.5.x`, `10.6.x`, `11.1.x`, `11.2.x`, `11.3.x`, `11.4.x`, `12.0.x`, …).
  Each branch:
  - `require: { "drupal/core": "~<minor>.0", "cweagans/composer-patches": "~1.7.0 || ~2.0" }`
    — the `require drupal/core ~<minor>.0` binds the release to that core minor (composer selects the
    matching release for the installed core).
  - `extra.patches."drupal/core"` — the curated core patches for that minor (two-line format), URLs
    pointing at the **`patches`** branch raw files.
  - The **`patches`** branch is a flat `.patch` file store (no per-core composer), referenced by
    `https://raw.githubusercontent.com/Vardot/drupal-core-patches/refs/heads/patches/<file>`.
- **`vardot/varbase-patches`** — the Composer plugin. **Requires** `vardot/drupal-core-patches`
  (`~10 || ~11 || ~12` on 9.1.x/9.2.x/10.0.x; `~11 || ~12` on 10.1.x/11.0.x). It no longer carries or
  restricts `drupal/core` patches. Its plugin allowlists `vardot/drupal-core-patches` so the core
  patches are applied — in **both** code paths (constant `VarbasePatchesPlugin::DEFAULT_ALLOWED_DEPENDENCY_PATCHES`
  used by the v1 `buildV1PatchesMap` and the v2 `FilteredDependencies` resolver).

### Per-Drupal-version patch switch (how the right set is chosen)
Consumer requires the broad range (`~10 || ~11 || ~12`). Each drupal-core-patches release `require`s
`drupal/core ~<minor>.0`, so Composer can only pick the release whose minor matches the installed
core → the site automatically gets the patch set for ITS Drupal core.

### Building/maintaining a core-minor set (from varbase-patches history)
1. Group varbase-patches tags by their `drupal/core` constraint
   (`git show <tag>:composer.json` → `require.drupal/core` + `extra.patches."drupal/core"`).
2. For a target core minor, take the **latest** varbase-patches tag whose constraint includes
   `~<minor>.0` and use its `drupal/core` patch set.
3. Download those patch files into the `patches` branch; point the new branch's composer URLs at them.
4. Create `<minor>.x` (off the nearest branch), set `require drupal/core ~<minor>.0` + the set, two-line
   format; copy docs/LICENSE/PR-template.
5. Tag `<minor>.0`.

### Releasing (CRITICAL)
- Tag semver **within the minor** (`11.3.0`, then `11.3.0.1`, `11.3.0.2` …).
- **Never move a tag** — Packagist rejects moved tags ("The last update failed"). For a re-release of
  an already-tagged commit, cut a **new** 4-segment tag (`11.3.0.1`), don't `git tag -f`.
- Packagist needs the GitHub webhook (`https://packagist.org/api/github` + the maintainer's API token)
  or a manual **Update** click; a metapackage's `patches` branch needs no composer/version.
- Future cores (`11.4.x`, `12.0.x`) are forward-compat placeholders: `require drupal/core ~<minor>.0`,
  **empty** `extra.patches."drupal/core"` until patches are re-rolled for that core.

## Standard issue / PR title

Same grammar as varbase-patches — `<Action> a patch for the <Target> on <ref>[ -- <reason>]` (Add /
Remove / Change / Update / Revert -) — but the Target is usually **`Drupal Core`** (occasionally a
recipe or library) and the core minor is the context:

- `Add a patch for Drupal Core on Issue #3543210: Quick Edit Save Via Contextual Links Redirects to
  404 Page`
- `Change a patch for Drupal Core on Issue #3326684: Fix PHP8.1+ Deprecated mb_strtolower() null - for
  Drupal 10.6.2`
- `Remove a patch for Drupal Core on Issue #3538500: Fix block plugin not found warnings during Drush
  installation - for Varbase 11.0.x`

Branch / release infra issues state the action directly, e.g. `Update Drupal Core from ~10.3.0 to
~10.4.0 for Varbase Patches`, `Restrict old list of Drupal core's patches to Drupal ~10.2.0 in the
9.2.x branch and release the 9.2.12 tag`.

The issue and its MR/PR share the exact title; the PR ends with the Checkpoints checklist.

Three reinforcements that apply on top of the grammar above:

1. **A re-roll of an existing core patch is a `Change` — never an ad-hoc `fix:`/`task:` prefix.** A
   re-roll keeps the ORIGINAL upstream commit-type and issue title, it does not invent a new one:
   `Change a patch for Drupal Core on <type>: #<nid> <full upstream issue title>`. The "why now" (new
   core minor, corrected diff, drift against the target branch) goes only in the optional
   `-- <reason>` suffix, e.g. `-- re-rolled against Drupal 11.4.2`. Match the title style already used
   in that branch's `CHANGELOG.md` — don't invent a style that contradicts the historical commits
   (see the `task:`/`revert:` entries in this branch's `CHANGELOG.md`, e.g. `task: patch drupal/core
   for #3606822 (ClassResolver synthetic kernel on install)`).
2. **A patch change split across branches/repos shares ONE canonical title.** When one change spans
   the `patches`-branch file PR + the core-minor-branch composer-repoint PR (or a companion PR in
   `vardot/varbase-patches`), the issue AND every PR carry the identical title — multiple PRs, one
   story, one title. Don't let the file PR and the wiring PR drift into different wording.
3. **`gh pr edit --title` gotcha.** It can fail with `Projects (classic) ... deprecated
   (repository.pullRequest.projectCards)` and silently NOT apply the title change — always verify
   after running it. Retitle via the REST API instead:
   `gh api -X PATCH repos/<owner>/<repo>/pulls/<n> -f title="..."`.

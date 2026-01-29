# Bulma Fork Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Fork Bulma CSS, merge upstream bugfixes, and customize aesthetics (colors, dark mode, shadows) for internal web app use.

**Architecture:** Thin fork with `upstream` remote tracking. Customizations live in `sass/overrides/` theme layer that imports Bulma's base and applies modifications, keeping upstream diff clean. Dark mode switches from `prefers-color-scheme` media query to `[data-theme]` attribute approach.

**Tech Stack:** Dart Sass, PostCSS/cssnano, npm

---

### Task 1: Fork and Clone Repository

**Files:**
- None (git operations only)

**Step 1: Fork the repo on GitHub**

Run:
```bash
gh repo fork jgthms/bulma --clone=false --fork-name bulma
```

Expected: Fork created at your GitHub account.

**Step 2: Clone the fork**

Run:
```bash
cd /Users/firdaus/Projects
gh repo clone bulma
cd bulma
```

Expected: Repo cloned locally.

**Step 3: Add upstream remote**

Run:
```bash
git remote add upstream https://github.com/jgthms/bulma.git
git fetch upstream
```

Expected: `upstream` remote added, branches fetched.

**Step 4: Verify remotes**

Run:
```bash
git remote -v
```

Expected: Both `origin` (your fork) and `upstream` (jgthms/bulma) listed.

**Step 5: Commit**

No commit needed — this is repo setup.

---

### Task 2: Verify Build Pipeline

**Files:**
- Read: `package.json`

**Step 1: Install dependencies**

Run:
```bash
cd /Users/firdaus/Projects/bulma
npm install
```

Expected: Dependencies installed without errors.

**Step 2: Run the full build**

Run:
```bash
npm run deploy
```

Expected: `css/bulma.css` and `css/bulma.min.css` generated. Build completes without errors or Sass deprecation warnings.

**Step 3: Verify output files exist**

Run:
```bash
ls -lh css/bulma.css css/bulma.min.css
```

Expected: Both files present, `bulma.css` ~763KB, `bulma.min.css` ~677KB.

**Step 4: Commit**

No commit needed — build artifacts are gitignored.

---

### Task 3: Cherry-Pick Merged Upstream Bugfixes

These PRs are already on `upstream/main`. Since we forked from `main`, they should already be included. Verify this.

**Step 1: Verify merged PRs are included**

Run:
```bash
git log --oneline | grep -i -E "(3940|3950|3963|3837|3906)"
```

Expected: Commits for PRs #3940, #3950, #3963, #3837, #3906 appear in the log. If any are missing, cherry-pick them from upstream:

```bash
git fetch upstream
git log upstream/main --oneline | grep -i "<PR_NUMBER>"
# then: git cherry-pick <commit-hash>
```

**Step 2: Rebuild and verify**

Run:
```bash
npm run deploy
```

Expected: Build succeeds.

**Step 3: Commit**

If any cherry-picks were needed:
```bash
git add -A
git commit -m "chore: verify upstream bugfixes included (PRs #3940, #3950, #3963, #3837, #3906)"
```

---

### Task 4: Cherry-Pick Open Upstream Bugfix PRs

Cherry-pick fixes from open PRs that haven't been merged upstream. Each PR lives on a contributor's fork branch.

**Step 1: Fetch and cherry-pick PR #4028 (Sass if() deprecation warnings)**

Run:
```bash
gh pr diff 4028 --repo jgthms/bulma > /tmp/pr-4028.patch
git apply /tmp/pr-4028.patch
npm run build-bulma
```

Expected: Patch applies cleanly, build succeeds.

**Step 2: Commit**

```bash
git add -A
git commit -m "fix: sass if() deprecation warnings (upstream PR #4028)"
```

**Step 3: Repeat for PR #4030 (navbar-item hover for buttons)**

```bash
gh pr diff 4030 --repo jgthms/bulma > /tmp/pr-4030.patch
git apply /tmp/pr-4030.patch
npm run build-bulma
git add -A
git commit -m "fix: navbar-item hover styles for button elements (upstream PR #4030)"
```

**Step 4: Repeat for PR #4012 (select icon hover)**

```bash
gh pr diff 4012 --repo jgthms/bulma > /tmp/pr-4012.patch
git apply /tmp/pr-4012.patch
npm run build-bulma
git add -A
git commit -m "fix: select icon brightening on label hover (upstream PR #4012)"
```

**Step 5: Repeat for PR #4001 (icon size specificity)**

```bash
gh pr diff 4001 --repo jgthms/bulma > /tmp/pr-4001.patch
git apply /tmp/pr-4001.patch
npm run build-bulma
git add -A
git commit -m "fix: icon size specificity within controls (upstream PR #4001)"
```

**Step 6: Repeat for PR #3839 (rounded button proportions)**

```bash
gh pr diff 3839 --repo jgthms/bulma > /tmp/pr-3839.patch
git apply /tmp/pr-3839.patch
npm run build-bulma
git add -A
git commit -m "fix: rounded button proportions for perfect circles (upstream PR #3839)"
```

**Step 7: Repeat for PR #3931 (remove vendor prefixes)**

```bash
gh pr diff 3931 --repo jgthms/bulma > /tmp/pr-3931.patch
git apply /tmp/pr-3931.patch
npm run build-bulma
git add -A
git commit -m "chore: remove vendor prefixes from sass (upstream PR #3931)"
```

**Step 8: Repeat for PR #3952 (remove outdated deps)**

```bash
gh pr diff 3952 --repo jgthms/bulma > /tmp/pr-3952.patch
git apply /tmp/pr-3952.patch
npm run build-bulma
git add -A
git commit -m "chore: remove outdated ruby-sass and node-sass deps (upstream PR #3952)"
```

**Step 9: Full rebuild and verify**

```bash
npm run deploy
```

Expected: Clean build, no warnings.

**Note:** If any patch fails to apply cleanly, inspect the diff manually (`gh pr diff NNNN --repo jgthms/bulma`), understand the intent, and apply the fix by hand. Commit each PR separately for clean history.

---

### Task 5: Create Override Theme Layer Structure

**Files:**
- Create: `sass/overrides/_index.scss`
- Create: `sass/overrides/colors.scss`
- Create: `sass/overrides/dark-mode.scss`
- Create: `sass/overrides/shadows.scss`
- Create: `sass/overrides/tokens.scss`
- Modify: `bulma.scss`

**Step 1: Create the overrides directory and index file**

Create `sass/overrides/_index.scss`:
```scss
// Bulma Fork: Custom overrides
// This layer applies aesthetic customizations on top of Bulma's base.
// Keep changes here to minimize upstream merge conflicts.

@forward "colors";
@forward "shadows";
@forward "dark-mode";
@forward "tokens";
```

**Step 2: Create placeholder override files**

Create `sass/overrides/colors.scss`:
```scss
// Color scheme overrides
// Redefines Bulma's default palette for app UI
```

Create `sass/overrides/dark-mode.scss`:
```scss
// Dark mode fixes and overrides
```

Create `sass/overrides/shadows.scss`:
```scss
// Shadow scale and dark-mode shadow variants
```

Create `sass/overrides/tokens.scss`:
```scss
// Semantic design tokens (surface, border, muted text, etc.)
```

**Step 3: Add override import to bulma.scss**

Modify `bulma.scss` to add the overrides import at the end:

```scss
// ... existing imports ...

// Fork overrides (must be last)
@use "sass/overrides";
```

**Step 4: Build to verify no breakage**

Run:
```bash
npm run build-bulma
```

Expected: Build succeeds (empty overrides should not affect output).

**Step 5: Commit**

```bash
git add sass/overrides/ bulma.scss
git commit -m "feat: add overrides theme layer structure"
```

---

### Task 6: Customize Color Scheme

**Files:**
- Modify: `sass/overrides/colors.scss`

**Context:** Bulma defines colors in `sass/utilities/initial-variables.scss` using HSL values. The defaults are highly saturated. We override by redefining the Sass variables before Bulma uses them, or by overriding the CSS custom properties in our theme layer.

The cleanest approach: override the CSS custom properties registered in the `:root`, since Bulma 1.0 uses CSS variables at runtime. This avoids touching upstream Sass files.

**Step 1: Define the app color palette**

Edit `sass/overrides/colors.scss`:
```scss
@use "../utilities/css-variables" as cv;

// App color palette: less saturated, refined tones for data-dense UI
// Each color is defined as HSL components to work with Bulma's palette generator

:root {
  // Primary: muted blue (less saturated than Bulma's default turquoise)
  --bulma-primary-h: 220;
  --bulma-primary-s: 60%;
  --bulma-primary-l: 50%;

  // Link: distinguishable from primary
  --bulma-link-h: 229;
  --bulma-link-s: 53%;
  --bulma-link-l: 53%;

  // Info: calm blue
  --bulma-info-h: 207;
  --bulma-info-s: 55%;
  --bulma-info-l: 50%;

  // Success: muted green
  --bulma-success-h: 153;
  --bulma-success-s: 48%;
  --bulma-success-l: 40%;

  // Warning: warm amber (keep higher saturation for visibility)
  --bulma-warning-h: 42;
  --bulma-warning-s: 90%;
  --bulma-warning-l: 52%;

  // Danger: muted red
  --bulma-danger-h: 4;
  --bulma-danger-s: 65%;
  --bulma-danger-l: 50%;
}
```

**Step 2: Build and visually inspect**

Run:
```bash
npm run build-bulma
```

Expected: Build succeeds. Colors in compiled CSS reflect overridden HSL values.

**Step 3: Commit**

```bash
git add sass/overrides/colors.scss
git commit -m "feat: override color palette with app-appropriate tones"
```

**Note:** These are starting values. Tune iteratively once you have actual UI to look at. The HSL values above are a reasonable starting point for a professional web app palette.

---

### Task 7: Fix Dark Mode

**Files:**
- Modify: `sass/overrides/dark-mode.scss`
- Modify: `sass/themes/_index.scss`

**Step 1: Switch dark mode activation from media query to data-attribute**

Read `sass/themes/_index.scss` first. It currently applies dark mode via `@media (prefers-color-scheme: dark)`. It also already supports `[data-theme=dark]` as a manual override. The fix is to remove the media query so dark mode is ONLY activated by the `[data-theme]` attribute, preventing FOUC.

Edit `sass/themes/_index.scss`: Remove or comment out the `@media (prefers-color-scheme: dark)` block. Keep the `[data-theme=dark]` and `[data-theme=light]` selectors.

**Step 2: Fix html background-color**

Edit `sass/overrides/dark-mode.scss`:
```scss
// Fix #2919: Remove background-color from html to prevent white flash.
// The body element handles background via Bulma's scheme-main variable.
html {
  background-color: transparent;
}

// Fix #3844: Explicit dark palette overrides
// Instead of relying on Bulma's automatic inversion, define explicit values
// for dark mode that produce better contrast and avoid muddy colors.
[data-theme="dark"] {
  // Scheme overrides for dark mode
  --bulma-scheme-main-l: 11%;
  --bulma-scheme-main-bis-l: 15%;
  --bulma-scheme-main-ter-l: 19%;

  // Text: higher contrast than Bulma default (71% -> 80%)
  --bulma-text-l: 80%;
  --bulma-text-weak-l: 60%;
  --bulma-text-strong-l: 93%;

  // Border: visible but subtle in dark mode
  --bulma-border-l: 25%;
  --bulma-border-weak-l: 20%;
}
```

**Step 3: Build and verify**

Run:
```bash
npm run build-bulma
```

Expected: Build succeeds. Dark mode styles only activate on `[data-theme="dark"]`, not on `prefers-color-scheme`.

**Step 4: Commit**

```bash
git add sass/overrides/dark-mode.scss sass/themes/_index.scss
git commit -m "fix: dark mode FOUC and explicit dark palette (fixes #3880, #2919, #3844)"
```

---

### Task 8: Implement Shadow Scale

**Files:**
- Modify: `sass/overrides/shadows.scss`

**Context:** Bulma defines one shadow value in `sass/themes/setup.scss` as a two-layer `hsla()` box-shadow. Components reference it via `cv.getVar("shadow")`. We add a shadow scale and dark-mode variants.

**Step 1: Define shadow scale**

Edit `sass/overrides/shadows.scss`:
```scss
// Shadow scale: sm through xl, with dark mode variants.
// Uses Bulma's shadow HSL variables for consistency.

:root {
  --bulma-shadow-sm:
    0 1px 2px 0 hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.05);

  --bulma-shadow-md:
    0 4px 6px -1px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.08),
    0 2px 4px -2px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.05);

  --bulma-shadow-lg:
    0 10px 15px -3px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.08),
    0 4px 6px -4px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.04);

  --bulma-shadow-xl:
    0 20px 25px -5px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.1),
    0 8px 10px -6px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.05);
}

// Fix #3886: Dark mode shadows need higher opacity and different color
// to be visible against dark backgrounds.
[data-theme="dark"] {
  --bulma-shadow-sm:
    0 1px 2px 0 hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.2);

  --bulma-shadow-md:
    0 4px 6px -1px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.25),
    0 2px 4px -2px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.15);

  --bulma-shadow-lg:
    0 10px 15px -3px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.25),
    0 4px 6px -4px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.12);

  --bulma-shadow-xl:
    0 20px 25px -5px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.3),
    0 8px 10px -6px hsla(var(--bulma-shadow-h), var(--bulma-shadow-s), var(--bulma-shadow-l), 0.18);
}
```

**Step 2: Build and verify**

Run:
```bash
npm run build-bulma
```

Expected: Build succeeds. New shadow variables appear in compiled CSS.

**Step 3: Commit**

```bash
git add sass/overrides/shadows.scss
git commit -m "feat: shadow scale (sm/md/lg/xl) with dark mode variants (fixes #3886)"
```

---

### Task 9: Add Semantic Design Tokens

**Files:**
- Modify: `sass/overrides/tokens.scss`

**Step 1: Define semantic tokens**

Edit `sass/overrides/tokens.scss`:
```scss
// Semantic design tokens for app UI.
// These extend Bulma's variable system with app-specific abstractions.

:root {
  // Surfaces: layered backgrounds for cards, modals, dropdowns
  --app-surface-0: var(--bulma-scheme-main);
  --app-surface-1: var(--bulma-scheme-main-bis);
  --app-surface-2: var(--bulma-scheme-main-ter);

  // Text
  --app-text-muted: var(--bulma-text-weak);
  --app-text-default: var(--bulma-text);
  --app-text-emphasis: var(--bulma-text-strong);

  // Borders
  --app-border: var(--bulma-border);
  --app-border-subtle: var(--bulma-border-weak);

  // Interactive states
  --app-focus-ring: var(--bulma-link);
  --app-hover-bg: var(--bulma-scheme-main-bis);
}
```

**Step 2: Build and verify**

Run:
```bash
npm run build-bulma
```

Expected: Build succeeds. Tokens appear in compiled CSS `:root` block.

**Step 3: Commit**

```bash
git add sass/overrides/tokens.scss
git commit -m "feat: add semantic design tokens for app UI"
```

---

### Task 10: Full Build Verification and Tag

**Step 1: Clean build**

Run:
```bash
npm run deploy
```

Expected: Full build (expanded + minified + versions) succeeds with no errors or warnings.

**Step 2: Verify output**

Run:
```bash
ls -lh css/bulma.css css/bulma.min.css
```

Expected: Files generated.

**Step 3: Inspect compiled CSS for overrides**

Run:
```bash
grep -c "app-surface" css/bulma.css
grep -c "shadow-sm" css/bulma.css
grep -c "data-theme" css/bulma.css
```

Expected: All return non-zero counts, confirming overrides are in the output.

**Step 4: Tag the release**

```bash
git tag -a v1.0.4-fork.1 -m "First fork release: upstream bugfixes + aesthetic customizations"
```

**Step 5: Push**

```bash
git push origin main --tags
```

---

### Task 11: Document Upstream Sync Process

**Files:**
- Create: `SYNC.md`

**Step 1: Write sync instructions**

Create `SYNC.md`:
```markdown
# Upstream Sync Process

## Monthly Check

1. Fetch upstream:
   ```bash
   git fetch upstream
   ```

2. Review new commits:
   ```bash
   git log upstream/main --oneline --since="1 month ago"
   ```

3. Cherry-pick relevant fixes:
   ```bash
   git cherry-pick <commit-hash>
   ```

4. Skip: docs changes, sponsor/branding, feature PRs that conflict with our overrides.

5. Rebuild and verify:
   ```bash
   npm run deploy
   ```

6. Check our diff is still clean:
   ```bash
   git diff upstream/main..main --stat
   ```

## If Upstream Releases a New Version

1. Fetch and review the release tag:
   ```bash
   git fetch upstream --tags
   git log upstream/main --oneline v1.0.4..upstream/main
   ```

2. Attempt merge (may conflict in `sass/themes/_index.scss` where we modified dark mode):
   ```bash
   git merge upstream/main
   ```

3. Resolve conflicts, prioritizing our overrides.

4. Rebuild and test.
```

**Step 2: Commit**

```bash
git add SYNC.md
git commit -m "docs: add upstream sync process"
```

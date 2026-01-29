# Bulma Fork

> Internal fork of [jgthms/bulma](https://github.com/jgthms/bulma) (MIT license).
> Aesthetic customizations for data-dense web app UI.

## Engineering Principles

1. **Minimize upstream divergence** — Keep customizations in `sass/overrides/`. Do not modify upstream Sass files unless absolutely necessary.
2. **Never trial-and-error loop** — If a Sass build fails, read the error, understand it, fix it. Don't guess.
3. **One commit per logical change** — Cherry-picked PRs get their own commit. Override changes get their own commit.

## Build Commands

```bash
npm run build-bulma    # Compile Sass → css/bulma.css
npm run deploy         # Clean + build all + minify all
```

Build output goes to `css/` (tracked in git). After Sass changes, run `npm run deploy` and commit the CSS output separately.

## Architecture

### Sass File Hierarchy

```
sass/
├── utilities/              # Variables, functions, mixins
│   ├── initial-variables.scss   # Base HSL colors, typography, spacing
│   ├── derived-variables.scss   # Computed theme values, color maps
│   ├── css-variables.scss       # CSS custom property registration
│   ├── controls.scss            # Control element sizing/focus
│   ├── functions.scss           # Color manipulation functions
│   ├── mixins.scss              # Reusable mixins
│   └── extends.scss             # Placeholder selectors
├── themes/                 # Theme definitions
│   ├── setup.scss               # Dynamic color computation, shadow definition
│   ├── light.scss               # Light theme values
│   ├── dark.scss                # Dark theme mixin
│   └── _index.scss              # Theme activation (MODIFIED: removed prefers-color-scheme)
├── base/                   # Reset, generic, animations
├── elements/               # Button, box, table, tag, etc.
├── form/                   # Input, select, checkbox, file
├── components/             # Navbar, card, dropdown, modal, etc.
├── grid/                   # Columns, CSS grid
├── layout/                 # Container, hero, section, footer
├── helpers/                # Utility classes
└── overrides/              # *** OUR CUSTOMIZATIONS ***
    ├── _index.scss              # Forwards all override modules
    ├── colors.scss              # Muted HSL palette for app UI
    ├── dark-mode.scss           # FOUC fix, explicit dark palette
    ├── shadows.scss             # Shadow scale (sm/md/lg/xl) + dark variants
    └── tokens.scss              # Semantic design tokens (--app-*)
```

### Variable Flow

```
Initial Variables → Derived Variables → CSS Custom Properties → Theme Setup
    → Light Theme (default) → Dark Theme ([data-theme="dark"] only)
    → Components → Our Overrides (last, wins by cascade)
```

### Override Layer (`sass/overrides/`)

All fork customizations live here. This is the only directory we actively modify. Benefits:

- `git diff upstream/main..main` shows exactly what we changed
- Upstream merges rarely conflict (only `sass/themes/_index.scss` is modified outside overrides)
- New upstream releases can be merged with minimal friction

### Dark Mode

Dark mode activates **only** via `[data-theme="dark"]` on the `<html>` element. The `prefers-color-scheme` media query was removed to prevent FOUC. The app must set the attribute via JavaScript.

### Shadow Scale

Custom shadow variables available: `--bulma-shadow-sm`, `--bulma-shadow-md`, `--bulma-shadow-lg`, `--bulma-shadow-xl`. These adapt automatically in dark mode.

### Semantic Tokens

App-level CSS variables prefixed with `--app-`:

| Token | Maps To |
|-------|---------|
| `--app-surface-0/1/2` | Layered backgrounds |
| `--app-text-muted/default/emphasis` | Text hierarchy |
| `--app-border/border-subtle` | Border variants |
| `--app-focus-ring` | Focus indicator color |
| `--app-hover-bg` | Hover background |

## Upstream Sync

See `SYNC.md` for the monthly sync process. Key points:

- Upstream remote: `git remote -v` shows `upstream` pointing to `jgthms/bulma`
- Fetch: `git fetch upstream`
- Cherry-pick relevant fixes, skip docs/branding
- Rebuild and verify after each sync

### Cherry-Pick Gotchas

- Some upstream PRs only modify compiled CSS, not Sass source. Find the actual Sass variable/mixin to change (e.g., PR #3978 edited `css/bulma.css` but the fix was in `sass/components/dropdown.scss`).
- Upstream PRs may have missing `@use` imports. Verify the file has the required `@use "sass:*"` declaration (e.g., PR #4006 needed `@use "sass:string"` added).
- Always run `npm run build-bulma` after each cherry-pick to catch errors before committing.

### Useful Sync Commands

```bash
gh pr list --repo jgthms/bulma --state open --limit 30 --json number,title,createdAt,changedFiles
gh pr view <number> --repo jgthms/bulma --json title,body,files,additions,deletions
gh pr diff <number> --repo jgthms/bulma
git fetch upstream refs/pull/<number>/head   # Fetch PR branch for cherry-pick
```

### Upstream PRs Applied

Cherry-picked from open upstream PRs (not yet merged by maintainer):

| PR | Description |
|----|-------------|
| #4030 | Navbar-item hover for buttons |
| #4012 | Select icon hover fix |
| #4001 | Icon size specificity |
| #3839 | Rounded button proportions |
| #3931 | Remove vendor prefixes |
| #3952 | Remove outdated deps |
| #3978 | Dropdown z-index fix (renders above navbar) |
| #3990 | Modern media range syntax for breakpoints |
| #4006 | Migrate `unquote()` to `string.unquote()` for Dart Sass 3.0 |
| #4028 | Migrate `if()` to modern CSS syntax for Dart Sass 1.95+ |

## Files Not to Modify

Prefer not to edit upstream files. When necessary for bugfixes or Sass compatibility, document the change in the PR table above. Currently modified upstream files:

- `sass/themes/_index.scss` — removed `prefers-color-scheme` media query
- `sass/components/dropdown.scss` — z-index fix (PR #3978)
- `sass/utilities/mixins.scss` — media range syntax (PR #3990), `if()` migration (PR #4028)
- `sass/utilities/functions.scss` — `if()` migration (PR #4028)
- `sass/utilities/css-variables.scss` — `string.unquote()` migration (PR #4006)
- `sass/helpers/typography.scss` — `if()` migration (PR #4028)

## Tech Stack

| Tool | Version | Purpose |
|------|---------|---------|
| Dart Sass | ^1.97 | Sass compiler |
| PostCSS + cssnano | ^7.1 | CSS minification |
| Node.js | — | Build runtime |

## Session Startup

```
1. [ ] Read this file
2. [ ] git status — check branch and state
3. [ ] git log upstream/main..main --oneline — see fork delta
```

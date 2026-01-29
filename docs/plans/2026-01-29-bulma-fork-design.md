# Bulma Fork Design

## Context

Bulma CSS (jgthms/bulma) is a 50k-star, MIT-licensed, pure-CSS framework maintained by a single developer. Post-1.0 (March 2024), development has slowed significantly: ~10 commits in 9 months, 339 open issues, 30 open PRs with slow review throughput. No notable active forks exist.

We are forking Bulma for internal use in a new web app project. The fork's purpose is to cherry-pick upstream bugfixes and improve aesthetics (color scheme, dark mode, shadows) for data-dense app UI.

## Viability

- **MIT license** -- no legal friction for private fork.
- **Modular Sass architecture** -- targeted modifications are straightforward.
- **Slow upstream cadence** -- low risk of rapid divergence or merge conflicts.
- **No migration cost** -- starting fresh, no existing Bulma projects.
- **Internal use only** -- no obligation for backward compatibility, docs, or community support.

**Main risk:** A burst of upstream activity would require reconciliation. Mitigated by keeping customizations in a separate theme layer with minimal structural changes.

## Branch Strategy

```
upstream/main  -- tracks jgthms/bulma:main (read-only, never commit here)
main           -- fork mainline with customizations on top of upstream
```

`git diff upstream/main..main` always shows exactly what we've changed.

## Phase 1: Fork Setup

- Fork jgthms/bulma on GitHub (private repo).
- Add upstream remote: `git remote add upstream https://github.com/jgthms/bulma.git`.
- Set up branch structure.
- Verify the Sass build pipeline works locally (`npm install`, `npm run build`).

## Phase 2: Upstream Bugfix Merge

### Already merged upstream (merge immediately)

| PR | Description |
|----|-------------|
| #3940 | Fix deprecated Sass `round()` functions |
| #3950 | Fix hero `is-bold` color variant syntax |
| #3963 | Fix outlined button color loading states |
| #3837 | Fix color brightness calculation |
| #3906 | Make CSS variables root configurable |

### Open PRs upstream (cherry-pick and test)

| PR | Description |
|----|-------------|
| #4028 | Fix Sass deprecation warnings for `if()` functions |
| #4030 | Fix navbar-item hover styles for button elements |
| #4012 | Fix select icon brightening on label hover |
| #4001 | Fix icon size specificity within controls |
| #3839 | Fix rounded button proportions (perfect circles) |
| #3931 | Remove vendor prefixes from Sass |
| #3952 | Remove outdated Ruby-Sass and Node-Sass dependencies |

### Process

- Apply merged PRs first (they're on upstream/main already).
- Cherry-pick open PRs one at a time from their source branches.
- Run Sass build after each batch to catch conflicts early.

## Phase 3: Aesthetic Customization

### 3a. Theme Layer Structure

Create `sass/overrides/` directory for all customizations. This directory imports Bulma's base and applies modifications on top, keeping the diff with upstream clean.

### 3b. Color Scheme

Bulma 1.0 uses HSL-based colors with CSS custom properties. The defaults are highly saturated and feel more suited to documentation sites than app UI.

**Changes:**
- Redefine base palette (primary, link, info, success, warning, danger) with less saturated, more refined tones.
- Override derived shade generation (light/dark variants) rather than relying on Bulma's auto-generation, which can produce muddy results.
- Add semantic tokens as CSS custom properties for app-specific uses: `--surface`, `--border`, `--text-muted`, etc.

### 3c. Dark Mode Fixes

Bulma's dark mode has several known issues:

| Issue | Problem | Fix |
|-------|---------|-----|
| #3880 | FOUC -- page flashes wrong theme on load | Replace `prefers-color-scheme` media query with `[data-theme="dark"]` attribute on `html` |
| #3886 | Shadows broken in dark mode | Define separate dark-mode shadow values (see 3d) |
| #3844 | No custom dark mode colors for user-defined colors | Define explicit dark palette values per color |
| #2919 | `background-color` on `html` causes white flash | Remove or conditionalize the `html` background-color rule |

**Approach:** Replace Bulma's automatic color inversion with explicit dark palette definitions. This produces better results than algorithmic inversion, which tends toward washed-out or muddy colors.

### 3d. Shadows

Bulma's shadows are minimal and do not adapt to dark mode.

**Changes:**
- Define a shadow scale as CSS custom properties: `--shadow-sm`, `--shadow-md`, `--shadow-lg`, `--shadow-xl`.
- Use darker, more diffuse shadows in dark mode.
- Apply consistently to cards, dropdowns, modals, and navbar.

### 3e. Testing

- Visual review of all major components: forms, cards, modals, tables, navbar, dropdowns, buttons, tags, notifications, messages.
- Test both light and dark modes.
- Test theme switching (no FOUC).

## Phase 4: Integration

### Package distribution

Publish as a private package via one of:
- npm private registry
- GitHub Packages
- Direct Git dependency (`"bulma-fork": "git+ssh://...#main"`)

### Upstream sync process

- Monthly: fetch upstream, review new commits and releases.
- Cherry-pick relevant bugfixes.
- Ignore docs, branding, and sponsor changes.
- Run full build and visual review after each sync.

## Out of Scope

- CSS output size optimization (not a priority for internal app).
- Community management, public docs, or branding.
- JavaScript components (Bulma is pure CSS, we keep it that way).
- Upstream feature PRs that conflict with our aesthetic direction (e.g., #3876 panel variants).

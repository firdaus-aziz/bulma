# Session Context - 2026-01-29T12:15:00+08:00

## Overview
- **Work**: Brainstorm, plan, and implement Bulma CSS fork with upstream bugfixes and aesthetic customizations
- **Status**: completed
- **Continues From**: Fresh start

## Git State
- **Branch**: `main`
- **Status**: clean (CSS files modified but gitignored)
- **Recent Commits**:
  - `ffdee724` chore: add Claude Code project setup
  - `1369c8ce` docs: add upstream sync process
  - `c36ab586` feat: add semantic design tokens for app UI
  - `0cc4f479` feat: shadow scale (sm/md/lg/xl) with dark mode variants (fixes #3886)
  - `1586f084` fix: dark mode FOUC and explicit dark palette (fixes #3880, #2919, #3844)

## What Was Done

### Phase 1: Brainstorming & Planning
- Analyzed Bulma repo health: 50k stars, solo maintainer, slow post-1.0 maintenance
- Researched fork landscape: no notable active forks, community sentiment mixed
- Identified dark mode issues: FOUC, shadows, custom color support
- Decided: thin fork with upstream remote, `sass/overrides/` layer, internal use only

### Phase 2: Infrastructure Setup
- Forked jgthms/bulma to firdavs/bulma
- Cloned to `/Users/firdaus/Projects/bulma/`
- Added `upstream` remote tracking jgthms/bulma
- Verified build pipeline (npm run deploy)

### Phase 3: Upstream Bugfixes
- 5 merged PRs already included (#3940, #3950, #3963, #3837, #3906)
- Cherry-picked 6 open PRs: #4030, #4012, #4001, #3839, #3931, #3952
- Skipped PR #4028 (requires Sass 1.95+, we use 1.94.2)

### Phase 4: Aesthetic Customization
- Created `sass/overrides/` directory with modular structure
- **Colors**: Override HSL palette with less saturated, app-appropriate tones
- **Dark Mode**: Removed `prefers-color-scheme` media query, now `[data-theme="dark"]` only. Fixed html background-color, added explicit dark palette.
- **Shadows**: Implemented 4-level scale (sm/md/lg/xl) with dark mode variants
- **Tokens**: Added 10 semantic CSS variables (`--app-surface-*`, `--app-text-*`, etc.)

### Phase 5: Project Setup for Claude Code
- Created `CLAUDE.md` with architecture, build commands, engineering principles
- Created `.claude/settings.json` with team permissions
- Updated `.gitignore` to exclude `.claude/settings.local.json`
- Moved design and implementation plans into `docs/plans/`

## Key Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Upstream sync | Thin fork with `upstream` remote | Minimize divergence, cherry-pick bugfixes |
| Override location | `sass/overrides/` directory | Clean separation, easy to merge upstream |
| Dark mode strategy | `[data-theme]` attribute only, not media query | Eliminates FOUC, app controls theme |
| Dark palette | Explicit values, not algorithmic inversion | Better contrast, avoids muddy colors |
| Shadow scale | 4 levels with dark variants | Provides flexibility, visible in both themes |
| Semantic tokens | `--app-*` aliases to Bulma vars | Stable app API, decoupled from Bulma internals |

## Build Status
- **Command**: `npm run deploy`
- **Result**: All CSS variants generated (bulma.css, bulma.min.css, no-dark-mode, no-helpers, prefixed variants)
- **Verification**: All overrides present in compiled CSS; `prefers-color-scheme` removed

## Files Changed
- `.claude/settings.json` (new)
- `CLAUDE.md` (new)
- `.gitignore` (updated)
- `docs/plans/2026-01-29-bulma-fork-*.md` (moved from parent)
- `sass/overrides/` — 5 new files (colors, dark-mode, shadows, tokens, _index)
- `bulma.scss` (1 line added: `@use "sass/overrides"`)
- `sass/themes/_index.scss` (prefers-color-scheme blocks removed)
- `SYNC.md` (new)
- 12 commits total: 6 bugfix cherry-picks + 5 override tasks + 1 project setup

## Next Steps (Prioritized)
1. **Push to origin**: `git push origin main --tags` (tag: v1.0.4-fork.1)
2. **First real project**: Use the fork in a web app
3. **Iterate on colors**: Tune HSL values based on actual UI usage
4. **Test dark mode**: Verify no FOUC, smooth theme switching
5. **Monthly upstream sync**: Check for new commits/releases

## Testing & Verification

```bash
# Verify build works
npm run deploy

# Verify overrides present in compiled CSS
grep -c "app-surface\|shadow-sm\|data-theme" css/bulma.css

# Verify prefers-color-scheme removed
grep -c "prefers-color-scheme" css/bulma.css  # Should be 0

# Verify git state
git log upstream/main..main --oneline  # Shows 12 fork commits

# Verify structure
ls -la sass/overrides/  # Should have _index, colors, dark-mode, shadows, tokens
```

## Architecture Summary
- **Thin fork**: Only `sass/overrides/` actively modified, minimal upstream divergence
- **Override layer**: CSS custom properties, no Sass variable rewrites
- **Dark mode**: `[data-theme]` attribute-based (no media query), explicit palette
- **Semantic tokens**: App-level aliases for stable API
- **Upstream sync**: Clean cherry-pick workflow via `SYNC.md`

## Known Limitations
- PR #4028 (Sass if() syntax) not applied — requires Sass 1.95+
- Color palette values are starting points — tune for your app's UI
- No JavaScript included — theme switching via `document.documentElement.setAttribute('data-theme', 'dark')`

## Important Files
- `CLAUDE.md` — Read first in next session
- `SYNC.md` — Monthly upstream maintenance
- `docs/plans/2026-01-29-bulma-fork-plan.md` — Full implementation details
- `sass/overrides/` — All customizations live here

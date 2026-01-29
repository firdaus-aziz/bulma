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

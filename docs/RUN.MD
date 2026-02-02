# RUN

## RUN-01

### Session Notes
- The repo discovery and cloning via `gh` worked smoothly once the batch clone finished; large upstream forks increased fetch output but did not block completion.
- Commit extraction across all repos completed without errors and produced a large but manageable dataset.
- The HTML heatmap and repo index render locally without a server by loading prebuilt JS data files.

### What Worked
- `gh` authentication already had `repo` scope, so private repositories were accessible.
- The commit log format was reliable for parsing messages and dates consistently.
- The UI layout remains readable on desktop and mobile with minimal load time.

### What Was Tricky
- Cloning all repositories took multiple passes due to timeouts.
- Repository sizes are reported as GitHub KB plus local disk usage, which can differ significantly for forked repos with large histories.

### Feedback / Suggestions
- If you want to keep this updated regularly, consider a scheduled script that re-runs the clone/fetch and data extraction steps.
- If you only want your authored commits (not all history), we can filter by author email or GitHub user id to reduce dataset size.
- For richer analytics, we can add per-repo timelines or split the heatmap by public vs private.

## RUN-02

### Session Notes
- Fork detection is supported by the GitHub CLI and used to filter the dataset.
- Regenerating the data without forks reduced the commit count significantly.
- The UI now shows forks in a separate section and keeps the heatmap focused on non-fork repos.

### What Worked
- `gh repo list --json isFork` provided reliable fork metadata.
- Filtering during data extraction kept the HTML logic simple.

### What Was Tricky
- None beyond ensuring the fork list remains visible while excluded from aggregation.

### Feedback / Suggestions
- If you want to include specific forks in the heatmap, we can add an allowlist to opt them back in.

## RUN-03

### Session Notes
- Documentation files were moved into a dedicated `docs/` directory.
- The commit extraction pipeline was rerun with commit bodies included for UI display.
- The day details section now groups commits by contiguous repo sequences and supports commit-body expansion.
- Heatmap and details navigation now jumps between sections and highlights the selected day.

### What Worked
- The data regeneration with `%b` commit bodies integrated cleanly into the existing JSON/CSV outputs.
- The new DOM grouping logic keeps the details panel readable while preserving repo context.

### What Was Tricky
- Cleaning non-ASCII separators in the UI required explicit replacements to keep files ASCII-only.

### Feedback / Suggestions
- If you want a per-repo timeline next, we can reuse the grouped data and add filters without re-extracting commits.

## RUN-04

### Session Notes
- The data files were intact; rendering failed due to corrupted JavaScript expressions in `index.html`.
- The corruption came from earlier global text replacements that unintentionally altered ternary operators.
- Fixing the operators restored the heatmap and repo lists without regenerating data.

### What Worked
- Quick verification of data file counts isolated the issue to the HTML/JS layer.

### What Was Tricky
- The corrupted operators were subtle because the file still loaded without syntax errors.

### Feedback / Suggestions
- Avoid global string replacements across mixed HTML/JS content; prefer targeted edits or apply_patch.

## RUN-05

### Session Notes
- Created a top-level `README.md` explaining purpose, workflow, updates, and limitations.
- Logged the change in TRN and RUN records.

### What Worked
- Simple README capture of the current architecture and update flow.

### What Was Tricky
- None.

### Feedback / Suggestions
- If you want reproducible updates, I can add a dedicated script and document it in the README.

## RUN-06

### Session Notes
- Found `Overview/` now contains the dashboard while `repos/` stayed at the root.
- Rewrote local repo paths in the data files to use `../repos/`.
- Updated README and logged the structural change.

### What Worked
- Direct data-file rewrites avoided needing to touch rendering logic in `index.html`.

### What Was Tricky
- None.

### Feedback / Suggestions
- If you plan to move folders again, we can add a small path-resolution helper in the UI to avoid hardcoded paths.

## RUN-07

### Session Notes
- Created a Git repo in `Overview/` with three backdated, thematic commits.
- Published the repo to GitHub as `nastajus-github-overview` and pushed `master`.

### What Worked
- Thematic commit grouping produced a cleaner narrative history than per-file commits.

### What Was Tricky
- None.

### Feedback / Suggestions
- If you want the default branch to be `main`, I can rename it and update the remote.

## RUN-08

### Session Notes
- Implemented a repo-focused heatmap filter state with visual highlighting and dimming.
- Added a clear filter control and wired repo names to activate filtering.
- Auto-scrolls the heatmap to the earliest year with commits for the selected repo.

### What Worked
- The filter state can be toggled without reloading or regenerating data.

### What Was Tricky
- None.

### Feedback / Suggestions
- If you want multi-repo filters or stacked comparisons, we can extend the filter logic.

## RUN-09

### Session Notes
- Added a day-focus interaction: clicking a square opens a popup and highlights relevant days in project colors.
- Implemented color assignment that avoids collisions for co-occurring projects when palette capacity allows.
- Added hover tooltips in the unfiltered heatmap with per-day project bullets.
- Increased the visibility of the clear filter button.

### What Worked
- Conic gradients provide a clear multi-project indicator without additional DOM elements.

### What Was Tricky
- Ensuring color uniqueness across co-occurring projects required a lightweight graph-coloring pass.

### Feedback / Suggestions
- If the maximum projects-per-day exceeds the palette, we can extend the palette or switch to patterned overlays.

## RUN-10

### Session Notes
- Added project-specific actions in the day popup to filter and show commits for one repo.
- Updated wording to reduce ambiguity when viewing all commits for a day.

### What Worked
- Reusing the existing repo filter keeps the UI consistent across views.

### What Was Tricky
- None.

### Feedback / Suggestions
- If you want an "Esc to clear" shortcut for day focus and filters, I can add it.

## RUN-11

### Session Notes
- Added `.gitignore` to suppress `.vscode/` noise.
- Adjusted project-click behavior in the day popup to keep focus on the heatmap.

### What Worked
- Repo filter already supports auto-scrolling to the earliest year, so reuse was straightforward.

### What Was Tricky
- None.

### Feedback / Suggestions
- If you want a one-click jump to details from the popup, we can add a separate action.

## RUN-12

### Session Notes
- Added GMU org repos under `../repos/[GMU]/` and included their commit history in the data.
- Added origin metadata to repo and commit records.
- Implemented a GMU heatmap color scale and origin-level filters in the repo list.

### What Worked
- Origin metadata makes filtering and labeling straightforward in the UI.

### What Was Tricky
- Mixed-origin days currently render in the GMU scale for visual clarity.

### Feedback / Suggestions
- If you want mixed-origin days to show a blended indicator, we can add a dual-color overlay.

## RUN-13

### Session Notes
- Backfilled TRN-02 with WizTree snapshot images and a brief summary.
- Logged the backfill request as TRN-13 and added ADR-11.

### What Worked
- Relative paths from `docs/` correctly reference the images in the Overview root.

### What Was Tricky
- None.

### Feedback / Suggestions
- If you want thumbnails instead of full-size embeds, I can add size-constrained HTML tags.

## RUN-14

### Session Notes
- Renamed WizTree images with a `wiztree-` prefix and kept them only under `docs/trn/`.
- Updated TRN-02 addendum references and added a new combined personal + GMU snapshot entry.

### What Worked
- Centralizing images under `docs/trn/` keeps TRN references consistent.

### What Was Tricky
- None.

### Feedback / Suggestions
- If you add future WizTree snapshots, place them under `docs/trn/` to keep links stable.

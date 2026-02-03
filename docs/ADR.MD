# ADR

## ADR-01

### Context
You asked for a local archive of all GitHub repositories (including private), raw commit data (messages, dates, sizes) exported to files, and a single-page HTML site that links to each repo (remote + local) and visualizes all commits as an interactive heatmap.

### Decision
- Use the GitHub CLI (`gh`) to enumerate repositories and access private repos with the existing authenticated token.
- Clone all repositories into a local `repos/` folder and keep them up to date with `git fetch`.
- Extract commit data from each repo with `git log --all --date=iso-strict`, store per-commit metadata, and aggregate by day for the heatmap.
- Write raw data into `data/commits.json`, `data/commits.csv`, `data/repos.json`, and `data/repos.csv`, using ASCII-safe escaping where needed.
- Generate `data/commits.js` and `data/repos.js` to allow `index.html` to load data without a local web server.
- Build `index.html` as a single-page, multi-year heatmap view with clickable day cells and a detail panel, plus a repository index that clearly flags private repos.

### Consequences
- The dataset is complete for all cloned repos but may be large (tens of thousands of commits).
- Private repos are clearly labeled in the UI while still remaining in local data files.
- The HTML works as a file-based page without requiring a server, because it loads data via prebuilt JS globals.

## ADR-02

### Context
The heatmap showed unexpected commit volume due to forked repositories (notably `processhacker`) being included in the aggregate.

### Decision
- Use `gh repo list --json isFork` to detect forked repositories.
- Exclude all forked repositories from commit extraction and heatmap aggregation.
- List forked repositories separately in the UI, after primary repositories, and label them as excluded.

### Consequences
- The heatmap now reflects only non-fork repositories.
- Forks remain visible for reference without polluting contribution counts.

## ADR-03

### Context
Quality-of-life updates were requested for navigation, daily detail rendering, and document organization, including showing commit bodies and better repo grouping.

### Decision
- Move ADR/TRN/RUN documentation into a dedicated `docs/` folder.
- Extend commit extraction to include commit bodies for per-commit display.
- Group daily commits by contiguous repo sequences, display repo headers once per sequence, and move private labels to the repo header.
- Add local navigation between heatmap and day details with animated highlighting of the selected day.

### Consequences
- The data files are regenerated with a `commit_body` field and updated CSV columns.
- The day details view is more compact and readable while preserving per-commit navigation to GitHub.
- Documentation files are centralized under `docs/`.

## ADR-04

### Context
The heatmap and repo lists rendered empty even though data files were present.

### Decision
- Trace the failure to corrupted JavaScript operators in `index.html`.
- Restore the intended ternary (`?:`) and logical (`&&`) expressions in the heatmap rendering code.

### Consequences
- The UI renders again using the existing data files.
- A follow-up note in docs records that global string replacements should avoid touching JS operators.

## ADR-05

### Context
The dashboard files were moved into an `Overview/` folder while repos stayed at the root, breaking local path references.

### Decision
- Update all local repo path references in data files to use `../repos/` relative to `Overview/`.
- Keep `index.html` and `data/` paths unchanged since they remain co-located in `Overview/`.

### Consequences
- Local repo links work again from `Overview/index.html`.
- Data files now reflect the new relative paths for any downstream use.

## ADR-06

### Context
A project-level focus mode was requested to visually isolate commit days for a selected repository.

### Decision
- Add a repository filter mode that highlights only the selected repo's commit days and dims all others.
- Provide a clear, persistent "Clear filter" control and auto-scroll to the earliest year with commits for that repo.
- Make repo names clickable in both Day Details and Repositories to activate the filter.

### Consequences
- Users can quickly isolate a single project's activity timeline without losing the full heatmap view.
- The UI now maintains an explicit filter state with a visible dismissal control.

## ADR-07

### Context
A project-focused visualization mode was requested to highlight all days for selected repositories with distinct colors and provide a day-level project popup.

### Decision
- Add a day-focus mode that assigns stable colors per project using a simple graph coloring pass (no same-color collisions for co-occurring projects when the palette allows).
- Show a day popup on square click listing projects with color swatches and actions (view commits, clear focus).
- Add hover tooltips in the unfiltered view that list per-day projects under the commit count.

### Consequences
- The heatmap can now display multi-project days using conic gradients (pie-like color wedges).
- If more unique co-occurring projects appear than the palette supports, colors may reuse on non-overlapping projects; a note is displayed.

## ADR-08

### Context
The day popup needed clearer project-specific actions, so selecting a project would filter the heatmap and show commits for that repo.

### Decision
- Make project names in the day popup clickable, applying the repo filter and rendering day details scoped to that project.
- Keep a separate "View all commits" action for the full-day list.

### Consequences
- Users can drill into a single project directly from the popup without ambiguity.

## ADR-09

### Context
VS Code workspace metadata should not show up as uncommitted noise, and project-click actions in the day popup should focus the heatmap instead of jumping to commit details.

### Decision
- Add `.vscode/` to `.gitignore` in the Overview repo.
- When clicking a project in the day popup, apply the repo filter and auto-scroll to the earliest year with commits, without jumping to the commit list.

### Consequences
- VS Code settings no longer appear as untracked changes.
- Project selection keeps users focused on the heatmap overview first.

## ADR-10

### Context
GMU organization repositories needed to be integrated into the dashboard with clear visual distinction and filtering.

### Decision
- Clone GMU repos into `../repos/[GMU]/` and include their commit history in the aggregated data.
- Add origin metadata (`origin_label`, `origin_type`) to repos and commits for filtering and display.
- Render GMU commit days with a red heatmap scale and allow origin-level filtering from the repo list.

### Consequences
- Days with GMU commits are visually distinct (red scale). Mixed-origin days currently render in GMU red to preserve clarity.
- Repo list includes origin labels that toggle an origin-wide filter on the heatmap and list.

## ADR-11

### Context
Two WizTree PNG snapshots needed to be embedded retroactively into TRN-02 to preserve original storage context.

### Decision
- Backfill TRN-02 with an addendum and embedded images (relative to `docs/`).

### Consequences
- TRN-02 now includes historical storage context without altering the original prompt text.

## ADR-12

### Context
WizTree snapshots were relocated under `docs/trn/` and renamed with a `wiztree-` prefix to avoid root-level duplicates and broken references.

### Decision
- Store all WizTree images only in `Overview/docs/trn/` and reference them from TRN.
- Update TRN-02 addendum and add a new TRN entry for the combined personal + GMU snapshot.

### Consequences
- Image references are now stable and scoped to the TRN documentation area.

## ADR-13

### Context
Clarity was needed on whether yearly totals include GMU + personal commits and how heatmap color levels are computed.

### Decision
- Add an in-page explanation describing the dynamic, dataset-relative color scaling and origin-specific handling.

### Consequences
- The heatmap now documents its scale assumptions directly in the UI.

## ADR-14

### Context
GMU day coloring used GMU-only counts for level selection, which made red intensity appear inconsistent against the global scale.

### Decision
- Use the same global daily commit count scale for both GMU and personal heatmap levels.

### Consequences
- Red and green intensities now map to the same global thresholds, improving cross-origin comparability.

## ADR-15

### Context
The yearly totals needed an origin breakdown on hover, and the dynamic heatmap scale needed explicit numeric ranges for verification.

### Decision
- Add a hover tooltip on yearly totals to show Nastajus vs GMU counts with color swatches.
- Display the computed level ranges at the end of the page using the current max daily commit count.

### Consequences
- Users can verify both origin composition and the dynamic scale thresholds directly in the UI.

## ADR-16

### Context
Tooltip popups needed to avoid overflowing the right edge, and work-history ranges needed to be represented on the heatmap.

### Decision
- Clamp tooltip and popup positions to stay within the viewport width.
- Store employment ranges in `data/work-ranges.json` and mark empty days in those ranges with a placeholder black square.
- Add a work-range legend and a GMU participation range summary.

### Consequences
- Popups no longer overflow the right edge.
- Work ranges are represented visually and documented in the UI.

## ADR-17

### Context
Work-range colors needed to follow a provided palette with dark UI-friendly squares and lighter legend text.

### Decision
- Use Set C (Dark UI) colors for work-range square backgrounds.
- Use Set B colors for legend text to increase contrast on the dark background.

### Consequences
- Work-range squares are distinct and readable in the heatmap; legend text remains high-contrast.

## ADR-18

### Context
The darkest job colors proved too heavy on the heatmap; SL | eONE also needed a purple (not blue) hue.

### Decision
- Move Paymentus, SL | eONE, and TF box colors to lighter Set B hues while keeping legend text as-is.

### Consequences
- Employment blocks are brighter and better differentiated on the dark background.

## ADR-19

### Context
Paymentus needed a less green, more blue hue and SL | eONE needed a darker purple while keeping TF unchanged.

### Decision
- Use Set A Paymentus blue (#0B2F6B) and Set A Secret Location purple (#7F768A) for the work-range fills.

### Consequences
- Paymentus now reads more blue; SL is darker and less vibrant, while TF stays the darker purple.

## ADR-20

### Context
Work-range background colors disappeared when filters or day focus were toggled.

### Decision
- Reapply work-range colors after any filter or focus change, while skipping commit days.

### Consequences
- Employment shading persists across interactive states.

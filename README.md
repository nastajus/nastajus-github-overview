# README

This folder is a local, file-based dashboard for your GitHub activity. It clones all repos, extracts commit history into data files, and renders an interactive all-years heatmap in `index.html` without needing a server.

## What This Is
- `index.html` renders a GitHub-style contribution heatmap across all years.
- Clicking a day jumps to detailed commits for that date.
- Clicking the date in Day Details jumps back to the heatmap and flashes the selected day.
- Forks are excluded from contribution counts but listed separately.
- Private repos are clearly marked.

## How It Works
1) Repo discovery uses `gh repo list` with your authenticated account.
2) All repos are cloned under `../repos/`.
3) Commit metadata is extracted via `git log --all --date=iso-strict`.
4) Data is written to `data/*.json` and `data/*.csv` plus browser-friendly `data/*.js` files.
5) `index.html` loads `data/commits.js` and `data/repos.js` as globals and renders the UI.

## Updating
To refresh the dashboard:
- Ensure `gh` is authenticated with `repo` scope.
- Re-run the data extraction script (the last used script is embedded in the session history) to regenerate:
  - `data/commits.json`, `data/commits.csv`, `data/commits.js`
  - `data/repos.json`, `data/repos.csv`, `data/repos.js`

If you want, I can add a dedicated update script to automate this.

## Data Files
- `data/commits.json` / `data/commits.csv`: commit metadata including subject and body
- `data/repos.json` / `data/repos.csv`: repo list with size info, privacy, and fork status
- `data/commits.js` / `data/repos.js`: same data packaged for the browser

## Limitations
- Forked repos are excluded from the heatmap by design.
- The page loads all data into memory; very large histories may slow rendering.
- Commit bodies are stored as plain text; any non-ASCII characters are escaped in JSON/CSV.
- Local links use relative paths, so `index.html` should be opened from this folder.

## Structure
- `index.html`: main UI
- `data/`: generated data files
- `../repos/`: cloned repositories (relative to this folder)
- `docs/`: ADR/TRN/RUN logs (in this folder)

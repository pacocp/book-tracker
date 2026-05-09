# Bookshelf

![Example of the page](https://github.com/pacocp/book-tracker/blob/main/screenshot.png)

A minimalist book-tracking app with a login screen. Add books, move them between *Reading Now*, *To Read*, and *Finished*, track your reading progress, and watch your reading journey grow.

## Features

- **Three categories**: Reading Now, To Read, Finished
- **Reading progress**: Track percentage complete for books you're reading
- **Read date**: Automatically recorded when you move a book to Finished
- **Shared password**: Simple authentication to keep your list private
- **Persistent storage** via `localStorage` + synced to a JSON file in the GitHub repo
- **Responsive** — works on desktop and mobile
- **Zero dependencies** — one HTML file, no build step

## Data storage

Books are stored in `localStorage` in your browser and automatically synced to `books.json` in the GitHub repo via the GitHub API. On page load, the app fetches the latest data from the repo. Changes are debounced and committed back 2 seconds after the last edit.

The JSON is organized by categories:

```json
{
  "reading": [{ "id": "...", "title": "...", ... }],
  "to-read": [...],
  "finished": [...]
}
```

## Setup

### 1. Run locally

```bash
python -m http.server 8000
# then visit http://localhost:8000
```

The password is configured via a placeholder — see deployment below for the recommended setup.

### 2. Deploying to GitHub Pages

1. Create a new repository on GitHub (e.g. `my-bookshelf`)
2. Push this project to the repository:

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/my-bookshelf.git
git push -u origin main
```

3. Go to **Settings → Secrets and variables → Actions → New repository secret** and add:

   - `BOOK_PASSWORD` — the shared password to access the app
   - `GH_PAT` — a [GitHub Personal Access Token](https://github.com/settings/tokens) with **Contents** → **Read and write** permissions (fine-grained) or `repo` scope (classic). This token is used by the app in the browser to push book changes back to `books.json`.

4. Go to **Settings → Pages** → **Source**: select **GitHub Actions**

The GitHub Action will automatically replace `YOUR_BOOK_PASSWORD` and `YOUR_GH_PAT` with your secrets when deploying.

## Tech

Vanilla HTML, CSS, and JavaScript. No frameworks, no build tools. Data stored in `localStorage` and synced to `books.json` in the repo via the GitHub Contents API.

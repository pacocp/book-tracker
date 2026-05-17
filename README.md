# Bookshelf

![Example of the page](https://github.com/pacocp/book-tracker/blob/main/screenshot.png)

A minimalist book-tracking app with a login screen. Add books, move them between *Reading Now*, *To Read*, and *Finished*, track your reading progress, and watch your reading journey grow.

## Features

- **Three categories**: Reading Now, To Read, Finished
- **Reading progress**: Track percentage complete for books you're reading
- **Read date**: Automatically recorded when you move a book to Finished
- **Shared password**: Simple authentication to keep your list private
- **Persistent storage** via `localStorage` + synced to Firestore
- **Responsive** — works on desktop and mobile
- **Zero dependencies** — one HTML file, no build step

## Data storage

Books are stored in `localStorage` in your browser and automatically synced to **Firestore** (Google's serverless NoSQL database). On page load, the app fetches the latest data from Firestore. Changes are debounced and saved 2 seconds after the last edit. Firestore is faster and more reliable than the GitHub Contents API — single roundtrip reads and writes, no token exposure, and built-in retry.

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
   - `FIREBASE_API_KEY` — your Firebase Web API key
   - `FIREBASE_AUTH_DOMAIN` — e.g. `your-project.firebaseapp.com`
   - `FIREBASE_PROJECT_ID` — your Firebase project ID
   - `FIREBASE_STORAGE_BUCKET` — e.g. `your-project.appspot.com`
   - `FIREBASE_MESSAGING_SENDER_ID` — your Firebase sender ID
   - `FIREBASE_APP_ID` — your Firebase app ID

4. Go to **Settings → Pages** → **Source**: select **GitHub Actions**

5. **Set up Firebase**:

   a. Go to the [Firebase Console](https://console.firebase.google.com/) and create a new project (or use an existing one).
   b. Register a **web app** in your Firebase project to get the Firebase config values.
   c. In the Firebase Console, go to **Firestore Database → Create database** and choose your preferred region (start in test mode).
   d. Copy your Firebase config (`apiKey`, `authDomain`, `projectId`, etc.) into **GitHub secrets** (step 3 above) — they'll be injected at deploy time.
   e. **Firestore Security Rules** — for a personal app protected by the shared password, set these rules in the Firebase Console → Firestore → Rules:

```firestore
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /books/{document} {
      allow read, write: if true;
    }
  }
}
```

## Migrating existing data from `books.json`

If you already have data in `books.json`, run the one-time migration script:

```bash
npm install firebase-admin
# Download your service account key from Firebase Console → Project Settings → Service Accounts
# Save it as service-account.json in the project root
node scripts/migrate-to-firestore.mjs
# Then delete books.json (it's no longer needed)
```

The GitHub Action will automatically replace `YOUR_BOOK_PASSWORD` with your secret when deploying.

## Tech

Vanilla HTML, CSS, and JavaScript. No frameworks, no build tools. Data stored in `localStorage` and synced to **Firestore**.

# Bookshelf

A minimalist book-tracking app with a login screen. Add books, move them between *Reading Now*, *To Read*, and *Finished*, track your reading progress, and watch your reading journey grow.

## Features

- **Three categories**: Reading Now, To Read, Finished
- **Reading progress**: Track percentage complete for books you're reading
- **Shared password**: Simple authentication to keep your list private
- **Persistent storage** via Supabase — your list survives anywhere
- **Responsive** — works on desktop and mobile
- **Zero dependencies** — one HTML file, no build step

## Setup

### 1. Create a Supabase project

1. Go to [supabase.com](https://supabase.com) and create a new project
2. Once created, open the **SQL Editor** in the left sidebar
3. Run the following SQL to create the database tables:

```sql
-- Books table
create table books (
  id uuid primary key default gen_random_uuid(),
  title text not null,
  author text default '',
  status text not null default 'to-read',
  progress integer default 0,
  created_at timestamptz default now()
);

-- Auth table (shared password)
create table auth (
  id text primary key default 'shared',
  password text not null
);

-- Insert your password (change 'mypassword' to whatever you want)
insert into auth (id, password) values ('shared', 'mypassword');

-- Enable row-level security
alter table books enable row level security;
alter table auth enable row level security;

-- Policies (allow anyone to read/write books; allow read for auth)
create policy "Allow all" on books for all using (true);
create policy "Allow read" on auth for select using (true);
```

### 2. Get your Supabase credentials

1. Go to **Settings → API** in your Supabase project
2. Copy the **Project URL** (e.g., `https://xyzabc.supabase.co`)
3. Copy the **anon public key** (the long string under "Project API keys")

### 3. Configure the app

Open `index.html` and replace the placeholder values at the top of the JavaScript section:

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';        // ← replace here
const SUPABASE_KEY = 'YOUR_SUPABASE_ANON_KEY';   // ← replace here
```

### 4. Run locally

```bash
python -m http.server 8000
# then visit http://localhost:8000
```

Enter the password you set in the SQL setup to access the app.

## Deploying to GitHub Pages

For automatic credential injection (recommended):

1. Create a new repository on GitHub (e.g. `my-bookshelf`)
2. Push this project to the repository:

```bash
git init
git add index.html README.md SPEC.md
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/my-bookshelf.git
git push -u origin main
```

3. Go to **Settings → Secrets and variables → Actions → New repository secret** and add:
   - `SUPABASE_URL` — your Supabase Project URL
   - `SUPABASE_ANON_KEY` — your anon public key

4. Go to **Settings → Pages** → **Source**: select **GitHub Actions**

5. Edit `.github/workflows/deploy.yml` and update the `sed` command if your branch name is different from `main`

The GitHub Action will automatically replace `YOUR_SUPABASE_URL` and `YOUR_SUPABASE_ANON_KEY` with your secrets when deploying.

## Tech

Vanilla HTML, CSS, and JavaScript. No frameworks, no build tools. Data stored in Supabase (PostgreSQL).
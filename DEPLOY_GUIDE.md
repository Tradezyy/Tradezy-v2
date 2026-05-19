# Deploying the M5 Journal Website

This folder is a **complete static website**. You publish it once and your friend can open the URL anytime to see your trading dashboard, even when your PC is off.

```
web/
├── index.html               ← the dashboard (single file)
├── data/
│   └── closed_trades.csv    ← your trades, auto-loaded on page open
└── DEPLOY_GUIDE.md          ← this file
```

Pick one of the two free hosting options below. Both serve the same `web/` folder. Both update automatically every time you push a new CSV to GitHub.

---

## Option 1 — GitHub Pages (recommended, simplest)

**One-time setup (~10 minutes):**

1. Create a GitHub account at <https://github.com> if you don't have one (free).
2. On the top-right click **+ → New repository**. Name it something like `mt5-journal`. Tick **Public** (Pages requires public on the free plan). Click **Create repository**.
3. On your PC, download and install **GitHub Desktop** from <https://desktop.github.com/> — it's the easiest way to push files. Log in with your GitHub account.
4. In GitHub Desktop, click **File → Clone repository**, pick `mt5-journal`, and choose where on your PC to put it (e.g. `Documents\mt5-journal`).
5. Open File Explorer to that folder. **Copy everything from this `web` folder** (the `index.html` file and the `data/` subfolder) **into** the `mt5-journal` folder you just cloned.
6. Back in GitHub Desktop you'll see the new files in the list. Type a summary like "first version" and click **Commit to main**. Then click **Push origin**.
7. On the repository page on GitHub, go to **Settings → Pages** (left sidebar).
8. Under "Build and deployment", set **Source** to "Deploy from a branch", **Branch** to `main`, and folder to `/ (root)`. Click **Save**.
9. Wait ~1 minute. The page refreshes and shows your URL — usually `https://YOURUSERNAME.github.io/mt5-journal/`. Bookmark this. Send it to your friend.

**Updating later:**

Whenever you have new trades:
1. In MT5, run your trade-exporter EA to produce a fresh `closed_trades.csv`.
2. Copy that file into `mt5-journal/data/closed_trades.csv` (overwriting the old one).
3. In GitHub Desktop: commit message → **Commit to main** → **Push origin**.
4. Wait ~1 minute. Your friend reloads the page → fresh dashboard.

A shortcut for that workflow lives in `publish_csv.bat` (in the parent `mt5_dashboard/` folder) — it copies the CSV from MT5's Files folder and runs the git commands for you in one click. Edit it once to point at your local repo path and you're done.

---

## Option 2 — Render (also free, slightly more clicks)

If you prefer Render, the steps are very similar:

1. Sign up at <https://render.com/> (free; you can sign in with your GitHub account).
2. Push your `web/` folder to GitHub exactly as in steps 1–6 of Option 1.
3. On Render's dashboard: **New + → Static Site**.
4. Connect your `mt5-journal` GitHub repo when prompted.
5. Build settings:
   - **Branch:** `main`
   - **Build command:** leave blank
   - **Publish directory:** `.` (a single dot — the repo root)
6. Click **Create Static Site**. After ~30 seconds you get a URL like `https://mt5-journal-xyz.onrender.com`.
7. Updates: same as GitHub Pages. Push new CSV → Render auto-deploys.

---

## Fully-automatic daily publishing (optional)

If you want the website to refresh **every day without clicking anything**:

1. Set up `publish_csv.bat` (see comment in the file — change the path at the top).
2. Open **Task Scheduler** in Windows (Start → search "Task Scheduler").
3. **Create Basic Task** → name it "MT5 Publish Journal".
4. Trigger: **Daily**, choose a time after your trading session (e.g. 22:00).
5. Action: **Start a program** → browse to `publish_csv.bat`.
6. Finish. Now every day at 22:00 your CSV gets pushed to GitHub, and your friend's bookmark reflects the latest numbers within ~2 minutes.

Requirement: MT5 terminal must be open and your EA must have written today's CSV before the scheduled task fires. Easiest: have the EA write the CSV on every trade close, then the scheduled task just publishes whatever's there.

---

## Privacy & security

- The dashboard runs **entirely in the browser** — no server, no backend, no database. Anyone with the URL can read the page, but you control exactly what's in the CSV.
- The CSV contains your trade history but **never your account number, broker password, or login credentials**. If you want to hide your broker name from your friend, edit `closed_trades.csv` to remove the magic numbers and comments before pushing.
- If you want the URL to be **private**: GitHub Pages doesn't natively support password protection on the free plan. The simplest workaround is to use a long random repo name like `mt5-journal-9k3xq2p` so the URL is effectively unguessable. For real auth, Render Pro or Cloudflare Access can put a login in front.

---

## Local testing

Before deploying, you can test `index.html` locally:

1. Open File Explorer to `web/`.
2. Double-click `index.html`. It opens in your browser.
3. The page tries to fetch `data/closed_trades.csv`. When opened directly as a file (`file://`), most browsers block this for security. You'll see "No CSV detected" — that's expected on `file://`.
4. Drag-and-drop your CSV onto the upload zone on the **Import CSV** page to test parsing.
5. Once you push to GitHub Pages / Render, the auto-fetch works (it's served over `https://`).

If you want auto-fetch locally too, run a tiny static server:
```
cd web
python -m http.server 8000
```
Then visit `http://127.0.0.1:8000/` — auto-fetch works fine over `http://`.

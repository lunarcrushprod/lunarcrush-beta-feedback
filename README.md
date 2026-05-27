# LunarCrush Beta Feedback

Closed-beta feedback form for new.lunarcrush.com testers. Static HTML page, no build step, hosted via GitHub Pages, writes to a Google Sheet via Apps Script.

**Live URL:** https://lunarcrushprod.github.io/lunarcrush-beta-feedback/ _(once Pages is enabled)_
**Optional custom domain:** feedback.lunarcrush.com

## Architecture

```
User → index.html (GitHub Pages)
              ↓ POST (no-cors)
       Apps Script web app
              ↓ appendRow
       Google Sheet (the database)
```

No backend to host. No API keys. Free.

## Deploying

### 1. Set up the Google Sheet + Apps Script

See `docs/apps-script-setup.md` for step-by-step. Takes ~5 minutes. You'll end up with an Apps Script deployment URL.

### 2. Wire the form to the Sheet

Open `index.html`, find this line near the bottom:

```javascript
const SCRIPT_URL = 'PASTE_YOUR_APPS_SCRIPT_URL_HERE';
```

Replace with your Apps Script web app URL. Commit and push.

### 3. Enable GitHub Pages

1. Go to **Settings → Pages**
2. Source: **Deploy from a branch**
3. Branch: **main** / folder: **/ (root)**
4. Save

Site goes live at `https://lunarcrushprod.github.io/lunarcrush-beta-feedback/` in about 60 seconds.

### 4. (Optional) Custom domain

1. In **Settings → Pages**, set custom domain to `feedback.lunarcrush.com`
2. At your DNS provider, add a CNAME record: `feedback` → `lunarcrushprod.github.io`
3. Wait for DNS propagation (usually 5-15 minutes)
4. Enable "Enforce HTTPS" once available

The `CNAME` file in the repo root already pins this for you.

## Files

- `index.html` — the form (single file, all CSS/JS inline)
- `CNAME` — custom domain config for GitHub Pages
- `docs/apps-script-setup.md` — Sheet + Apps Script setup guide
- `docs/email-templates.md` — invitation emails (Jon, Joe, Dan, Nico variants)

## Updating

Edit `index.html` locally, commit, push. Pages redeploys automatically in under a minute.

## Killing it after the beta

When the test is done:
1. Disable Pages in Settings (or just archive the repo)
2. Pause or delete the Apps Script deployment
3. The Sheet stays intact as the record of feedback

## Repo metadata

- **Created:** May 2026
- **Purpose:** Closed beta of new.lunarcrush.com
- **Tested by:** Power users, investors, friends and family
- **Beta window:** One week from launch email

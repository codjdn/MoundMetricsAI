# Mound Metrics AI — deploy to GitHub Pages (free, no server)

This site is 100% static — three HTML files, no backend, no build step.

## 1. Turn on analytics (optional but you asked for user counts)

1. Go to https://www.goatcounter.com/ and create a free account.
2. Pick a site code, e.g. `moundmetrics` (your dashboard will be at
   `moundmetrics.goatcounter.com`).
3. In all three files (`index.html`, `privacy.html`, `disclaimer.html`), find this line:
   ```html
   <script data-goatcounter="https://YOUR-CODE.goatcounter.com/count" async src="//gc.zgo.at/count.js"></script>
   ```
   and replace `YOUR-CODE` with your site code.
4. That's it — visit your GoatCounter dashboard any time to see visits/uniques.
   No cookies, no login for your users, nothing else to configure.

## 2. Push to GitHub

```bash
cd mound-metrics-ai        # this folder
git init
git add .
git commit -m "Initial site"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/mound-metrics-ai.git
git push -u origin main
```

(Create the empty repo on GitHub first at github.com/new — public repo, no README/license needed since you already have files.)

## 3. Turn on GitHub Pages

1. On GitHub, go to your repo → **Settings** → **Pages**.
2. Under "Build and deployment", set **Source** to `Deploy from a branch`.
3. Set **Branch** to `main`, folder `/ (root)`. Save.
4. Wait about a minute, then GitHub shows your live URL:
   `https://YOUR-USERNAME.github.io/mound-metrics-ai/`

That's the whole deploy. Every time you `git push` a change, the live site updates automatically in about a minute — no rebuild step, no server to manage.

## 4. Optional: custom domain

In the same **Settings → Pages** screen, add your domain under "Custom domain."
GitHub gives you a CNAME record to add at your domain registrar. HTTPS is handled
automatically once it's verified.

## Files

- `index.html` — the app itself (unchanged functionality, plus a footer, a
  one-time disclaimer banner, and the analytics snippet)
- `privacy.html` — privacy policy
- `disclaimer.html` — medical disclaimer
- Everything else (video processing, pose detection, storage) still runs
  entirely in the browser — nothing new was added server-side because there
  is no server.

## A note on the legal pages

I drafted the disclaimer and privacy policy to accurately describe what the
app does (nothing is uploaded, all processing and storage is local, analytics
is anonymous page-view counting only). I'm not a lawyer — if you plan to
promote this beyond casual/personal use, it's worth a quick review by one,
especially given the youth-sports and injury-risk angle.

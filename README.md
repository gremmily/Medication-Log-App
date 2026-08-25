# Med Log — setup

Two files. `index.html` is the whole app; `sw.js` is optional and only adds offline support.

## 1. Put the files on a web host

Upload `index.html` and `sw.js` **side by side in the same folder** on any static host — GitHub Pages, Netlify Drop, Cloudflare Pages, or your own web space.

Two requirements:

- The URL must be **HTTPS**. iOS won't save data or run the offline worker on plain `http://`.
- The URL should be **stable**. Data is tied to the exact domain, so if the address changes later, the app looks empty (your backup file restores it — see below).

If you don't already have a host: create a free GitHub account, make a new **public** repository, drag both files into it, then go to Settings → Pages → set Source to "Deploy from a branch" / `main` / `/ (root)`. In a minute or two your app is at `https://YOURNAME.github.io/REPONAME/`. Public repo means someone who guesses the URL can see the *app* — it does **not** expose your data, which never leaves your phone.

## 2. Add it to your iPhone

1. Open the URL in **Safari** (this doesn't work from Chrome on iOS).
2. Tap the **Share** button, then **Add to Home Screen**.
3. Name it and tap Add.

It now has its own icon and opens full-screen with no browser bar.

## 3. Using it

- **Today** — tap a saved medication to log a dose at the current time. "Log a dose" opens the full form for anything one-off or backdated.
- **History** — filter by date range, search names and notes, tap any entry to edit or delete it.
- **Meds** — your saved medications and their usual dosages. These become the one-tap buttons.

## Export

On the History tab, the export button saves a CSV of exactly what's currently filtered on screen. Safari will offer to save it to Files or send it somewhere. Columns: Date, Time, Medication, Dosage, Notes, and a full ISO timestamp for sorting.

## About your data

Everything lives in Safari's local storage on your phone. Nothing is uploaded, there's no account, and no one else can see it — including whoever hosts the files.

The trade-off is that it's only in one place. Use **Save backup file** on the Meds tab now and then and keep the `.json` somewhere safe; **Restore from backup** reads it back. Worth doing before you update iOS or clear Safari data.

## Updating the app later

Replace `index.html` on your host. Your logged data survives — it's stored separately from the file. If you're using `sw.js`, also change the line `const CACHE = "medlog-v1";` to `"medlog-v2"` so phones fetch the new version instead of the cached one.

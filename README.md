# Med Log — setup

Two files. `index.html` is the whole app; `sw.js` is optional and only adds offline support.

There are **two separate repos** in this setup, and it matters that they stay separate:

| | what it holds | visibility |
|---|---|---|
| the **app** repo | `index.html`, `sw.js` | public (GitHub Pages needs it on the free plan) |
| the **data** repo | your log, as one JSON file | **private** |

The app repo is just the program — no medical data ever goes in it.

---

## 1. Publish the app

Upload `index.html` and `sw.js` side by side into a public repo, then Settings → Pages → Source: "Deploy from a branch" → `main` → `/ (root)`. In a minute or two the app is live at `https://YOURNAME.github.io/REPONAME/`.

The URL must be **HTTPS** (iOS won't persist data otherwise) and should stay **stable** — your local data is keyed to the exact address.

## 2. Add it to your iPhone

Open the URL in **Safari** (not Chrome), tap **Share** → **Add to Home Screen**. It gets its own icon and opens full-screen.

At this point it works, but only stores data on that phone. Step 3 fixes that.

## 3. Turn on sync

**Create the private data repo:** [github.com/new](https://github.com/new) → name it something like `med-log-data` → set it to **Private** → Create. Leave it empty; the app creates the file itself. (Tick "Add a README" if you'd rather not stare at an empty-repo page.)

**Create a scoped token:** [Settings → Developer settings → Personal access tokens → Fine-grained tokens](https://github.com/settings/personal-access-tokens/new).

- **Repository access** → Only select repositories → pick just `med-log-data`
- **Permissions → Repository permissions** → **Contents: Read and write**. Nothing else — no other permission is needed and none should be granted.
- **Expiration** — GitHub will make you re-issue it when it lapses. Pick a long window and note the date, or the app will start showing "Sync error" out of nowhere.

Copy the token (it's shown once).

**Connect:** in the app, Meds tab → **Set up sync** → paste `YOURNAME/med-log-data`, branch `main`, path `med-log.json`, and the token → Connect.

## 4. Add your PC

Open the same URL in your desktop browser, go to Meds → Set up sync, and enter the *same* four values. Both devices now read and write the same file.

You can add a doses from either one. If both have been edited, the newer edit of each individual entry wins — you won't lose one device's work because the other synced later.

---

## How sync behaves

The chip in the top-right is the whole status display:

- **Synced** — everything is in the repo
- **Pending** — you just made a change; it pushes a couple of seconds later
- **Syncing…** / **Offline** / **Sync error** — tap it to jump to details and retry

Changes push a few seconds after you make them, and the app pulls when you open it, when you switch back to it, and every 90 seconds while it's on screen. Log a dose in a dead zone and it saves locally and goes up when you have signal again.

Deleting an entry leaves a hidden tombstone in the file so the deletion reaches your other device rather than the entry reappearing. That's why the JSON has slightly more records than the app shows.

## Your data

The private repo is the durable copy — and because it's git, every change is a commit, so you can see the file's whole history on GitHub and recover an earlier version if something goes wrong.

The **Save backup file** button on the Meds tab still works and is worth using occasionally, especially before you change anything about the setup. **Restore from backup** merges rather than overwrites — nothing already present is lost.

A caution about the token: it lives in that browser's storage on that device. It only reaches one private repo, but anyone who can unlock your phone could read it out. If you lose the device, revoke the token on GitHub and the access is gone.

## Export

History tab → export gives you a CSV of exactly what's currently filtered. Columns: Date, Time, Medication, Dosage, Notes, ISO timestamp.

## Updating the app later

Replace `index.html` in the app repo. Your data is untouched — it lives in the other repo and in browser storage, not in the file. If you're using `sw.js`, also bump `const CACHE = "medlog-v1"` to `"medlog-v2"` so phones fetch the new version rather than the cached one.

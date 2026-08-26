# Med Log

A private medication log that runs as a web app on an iPhone home screen. No app store, no account, no server of ours. Data lives on the device and is backed up to a private GitHub repo you own.

## The two repos

| | holds | visibility |
|---|---|---|
| **app** repo | `index.html`, `sw.js` | public (GitHub Pages needs it on the free plan) |
| **data** repo | the log, as one JSON file | **private** |

No medical data ever goes in the app repo.

---

## Setting up the first device

1. **Publish the app.** Push `index.html` and `sw.js`, then Settings → Pages → Deploy from a branch → `main` → `/ (root)`. The app appears at `https://YOURNAME.github.io/REPONAME/`. HTTPS is required and the URL must stay stable.
2. **Add to the phone.** Open the URL in **Safari**, tap Share → **Add to Home Screen**. The app shows a banner reminding you to do this.
3. **Create the private data repo.** [github.com/new](https://github.com/new), name it e.g. `med-log-data`, set **Private**. Leave it empty — the app creates the file.
4. **Create a token.** [Fine-grained personal access token](https://github.com/settings/personal-access-tokens/new) → Repository access: only `med-log-data` → Permissions → Repository permissions → **Contents: Read and write**, nothing else. Note the expiry date; when it lapses the app just starts saying "Sync error".
5. **Connect.** Meds tab → Set up sync → paste the repo, branch `main`, path `med-log.json`, and the token.

## Setting up any device after that — no typing

On a device that already syncs: **Meds → Set up another device**. That builds a link carrying the repo details and token. Send it to the person, they open it in Safari, and the app configures itself and wipes the details out of the address bar. They add it to their home screen and they're done — they never see a token, and any medications already in the log arrive on their first sync.

The link can also pre-set **larger text** and the **name for their printed summary**, so the phone arrives set up the way you want it.

**Treat the link like a password.** It grants read/write to that one private repo and nothing else. Send it one-to-one, confirm it worked, then delete the message on both sides. Give each person their own token so you can revoke one without breaking the other.

## Using it

- **Today** — saved medications as one-tap buttons showing when each was last taken. "Log a dose" opens the full form for anything one-off or backdated.
- **History** — a doses-per-day chart, date-range filters, search, and every entry tappable to edit or delete. The chart switches to weekly bars past a month and monthly past four, so it stays readable.
- **Meds** — the saved medication list, display settings, sync status, and data tools.

## Getting data out

- **Export CSV** — exactly what's currently filtered. Columns: Date, Time, Medication, Dosage, Notes, ISO timestamp.
- **Printable summary** — a one-page report: totals per medication with first and last dose, then the full chronological log. On iPhone the print sheet offers Share → Save to Files to keep it as a PDF.

## How sync behaves

The chip top-right is the whole status display: **Synced**, **Pending**, **Syncing…**, **Offline**, **Sync error**. Tap it for details and to retry.

Changes push a few seconds after you make them. The app pulls on open, on refocus, and every 90 seconds while on screen. Log a dose with no signal and it saves locally and goes up later.

Each record carries a timestamp, so the newer edit of any given entry wins — a device syncing late can't overwrite the other's work. Deletions leave a tombstone in the file so they actually propagate; that's why the JSON holds a few more records than the app shows.

## Your data

The private repo is the durable copy, and because it's git you get a full version history — every change is a commit you can inspect or roll back on GitHub.

**Save backup file** on the Meds tab writes a JSON snapshot; **Restore from backup** merges it back without losing anything already present. Worth doing before you change the setup.

The app also asks iOS for persistent storage so Safari is less likely to evict the local copy.

## Developing

Line endings are normalised by `.gitattributes` — real diffs only.

Updating the app is just replacing `index.html`; the data lives elsewhere and is untouched. If you're using `sw.js`, bump `const CACHE = "medlog-v1"` to `v2` so phones fetch the new version instead of the cached one.

# HYNM Gantt Chart (GitHub Pages)

An online, multi-user editable Gantt chart deployed as a static site on GitHub Pages, with a password gate and direct save-back to the repository.

## Features
- 📊 Day / Week / Month views, with today line and weekend dimming
- ➕ Add / edit / delete tasks; progress slider; color by phase
- 🔒 Password gate (SHA-256, default password `HYNM2026`)
- 💾 One-click **Save & Upload** writes changes back to the repo via the GitHub Contents API (no config dialog — GitHub config is hard-coded)
- 📥 Export / 📤 Import JSON for backup
- 📱 Responsive layout: full desktop experience, collapsible side panel on mobile (≤900px / ≤560px breakpoints)

## Deploy
1. Create a public repo on GitHub, e.g. `HYNM_gantt-chart`.
2. Upload **`index.html`** and **`gantt-data.json`** to the repo root (do **not** upload `gantt-data.en.json` — that is the local/reference copy).
3. Settings → Pages → Source: `Deploy from a branch` → Branch: `main` / `(root)` → Save. Wait ~1 minute.
4. Visit `https://<your-username>.github.io/<repo>/`, enter the password `HYNM2026`, and the Gantt chart loads.
5. Click **💾 Save & Upload** after editing — changes are encrypted-in-transit and written back to `gantt-data.json`; refresh to see the latest data.

## GitHub configuration (hard-coded)
The repo owner, repository name, branch, Personal Access Token and data file path are hard-coded in `index.html` as `GITHUB_CONFIG`:

| Field | Value |
|-------|-------|
| owner | `kamille-tsui` |
| repo | `HYNM_gantt-chart` |
| branch | `main` |
| token | `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` |
| path | `gantt-data.json` |

> ⚠️ The token is stored in the client-side HTML as requested (so no config dialog is needed). For a public repo this means the token is visible to anyone who opens the page source, so **use a fine-grained token with `Contents: Read and Write` scoped only to this repo, and rotate it if it is ever leaked.** For higher security, host the page on an internal/private server with server-side authentication instead.

## Change the password
1. Compute the SHA-256 hex digest of the new password, e.g.:
   ```bash
   echo -n 'YourNewPassword' | sha256sum
   ```
2. In `index.html`, replace the value of `PASSWORD_HASH` with the new hex digest.
3. Commit and push; the page now requires the new password.

## Notes
- The Gantt chart reads `gantt-data.json` from the repo root. Keep only this one data file in the repo root to avoid format conflicts.
- `fetch(..., { cache: 'no-store' })` is used so GitHub Pages / CDN caching does not serve stale data after a save.
- The date system uses UTC ordinals, so task bars align correctly with the timeline header across all time zones and for dates from 2026 through 2099.

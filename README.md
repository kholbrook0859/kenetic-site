# Kenetic, LLC company site

A one-page identity site for [keneticsolutions.co](https://keneticsolutions.co).
It is plain HTML and CSS, has no build step, and uses no external dependencies.

## Local preview

From this folder in PowerShell:

```powershell
python -m http.server 4173 --bind 127.0.0.1
```

Then open `http://127.0.0.1:4173/`.

## Files

- `index.html` — page structure, company copy, and metadata
- `styles.css` — layout, responsive rules, and visual design
- `assets/harbor-lighthouse.svg` — approved Harbor lighthouse mark
- `vercel.json` — zero-build Vercel preset and response security headers
- `DEPLOY-RUNBOOK.md` — owner-executed repository, deployment, and domain steps
- `evidence/` — local 390px and 1440px preview captures (not committed)

## Contact note

The company-inquiries line intentionally reuses
`support@harborfinancialapp.com` as the current shared contact. Replace it in
both visible text and the matching `mailto:` link if Kenetic adopts a dedicated
company inbox.

# Kenetic company site — owner deploy runbook

This runbook is for Ken to execute. Creating a Git repository and Vercel
project, changing DNS, and making the production site live are owner actions.

## 1. Confirm the local files

Open PowerShell and run:

```powershell
Set-Location -LiteralPath 'C:\KENETIC\kenetic-site'
python -m http.server 4173 --bind 127.0.0.1
```

Open `http://127.0.0.1:4173/`, review the page, then stop the server with
`Ctrl+C`.

Before publishing, decide whether the company-inquiries line should continue
to reuse `support@harborfinancialapp.com` or be replaced with a dedicated
Kenetic inbox. If it changes, update both the visible address and its `mailto:`
link in `index.html`.

## 2. Create the repository

In GitHub:

1. Choose the intended Kenetic organization or owner account.
2. Click **New repository**.
3. Set **Repository name** to `kenetic-site`.
4. Choose the visibility Kenetic wants. Do not initialize the repository with a
   README, `.gitignore`, or license because the local folder already has files.
5. Click **Create repository** and copy the HTTPS repository URL GitHub shows.

Back in PowerShell, run these commands. Replace `<GITHUB-REPOSITORY-URL>` with
the copied HTTPS URL; do not type the angle brackets.

```powershell
Set-Location -LiteralPath 'C:\KENETIC\kenetic-site'
git init -b main
git add -- .gitignore index.html styles.css vercel.json README.md DEPLOY-RUNBOOK.md assets/harbor-lighthouse.svg
git commit -m "Launch Kenetic company site"
git remote add origin <GITHUB-REPOSITORY-URL>
git push -u origin main
```

## 3. Create the Vercel project in the KENETIC workspace

Vercel's current dashboard flow is documented at
<https://vercel.com/docs/projects/managing-projects> and its Git import behavior
at <https://vercel.com/docs/git>.

1. Sign in at <https://vercel.com/dashboard>.
2. Use the scope switcher at the upper left to select the **KENETIC** workspace.
   Stop if a personal scope is selected.
3. Click **Add New…** and then **Project**.
4. Find `kenetic-site` under the connected Git provider and click **Import**.
   If it is not listed, use **Configure GitHub App** and grant the KENETIC
   workspace access to this repository only, then return to import it.
5. Set **Project Name** to `kenetic-site`.
6. Leave **Root Directory** as `.`.
7. Set **Framework Preset** to **Other**.
8. Expand **Build and Output Settings**:
   - enable the **Build Command** override and leave the value empty;
   - leave **Output Directory** at the framework default (`.` because there is
     no `public` folder);
   - do not add an Install Command or environment variables.
9. Click **Deploy** once.
10. Wait for the deployment status to become **Ready**, open the generated
    `*.vercel.app` URL, and verify the company name, Harbor link, both contact
    links, footer, and the lighthouse image before attaching the domain.

Vercel documents the zero-build static configuration at
<https://vercel.com/docs/builds> and
<https://vercel.com/docs/builds/configure-a-build>.

## 4. Attach `keneticsolutions.co`

Current measured state on 2026-08-27: the domain uses Cloudflare authoritative
nameservers, the apex has no A/AAAA/CNAME web record, and `www` does not exist.
Its existing MX and TXT records are separate from web routing and must remain
untouched.

1. In the Vercel `kenetic-site` project, open **Settings → Domains**.
2. Click **Add Domain**, enter `keneticsolutions.co`, and add it to the project.
3. Also add `www.keneticsolutions.co` when Vercel offers it.
4. Keep `keneticsolutions.co` as the primary production domain and configure
   `www.keneticsolutions.co` to redirect to it.
5. Copy the exact apex A-record value and `www` CNAME value Vercel displays.
   Use those displayed values even if examples elsewhere differ.

In Cloudflare:

1. Open the `keneticsolutions.co` zone and choose **DNS → Records**.
2. Do not edit or delete the existing MX record or Google site-verification TXT
   record.
3. Add the apex record Vercel requested:
   - **Type:** `A`
   - **Name:** `@`
   - **IPv4 address:** the exact value shown by Vercel
   - **Proxy status:** **DNS only** (gray cloud) for initial verification
   - **TTL:** Auto
4. Add the `www` record Vercel requested:
   - **Type:** `CNAME`
   - **Name:** `www`
   - **Target:** the exact value shown by Vercel
   - **Proxy status:** **DNS only** (gray cloud) for initial verification
   - **TTL:** Auto
5. Save both records.

Return to **Vercel → Project → Settings → Domains**. Wait for both domain rows
to show **Valid Configuration** and for the SSL certificate to be ready. Do not
change Cloudflare nameservers; Cloudflare remains the DNS provider.

Vercel's current custom-domain instructions are at
<https://vercel.com/docs/domains/working-with-domains/add-a-domain> and its
external-DNS guidance at
<https://vercel.com/docs/domains/set-up-custom-domain>.

## 5. Production verification

After Vercel reports both domains ready:

1. Open `https://keneticsolutions.co/` in a private browser window.
2. Confirm it loads over HTTPS and shows **Kenetic, LLC**.
3. Open `https://www.keneticsolutions.co/` and confirm it redirects to the apex
   site.
4. Click **Visit Harbor** and confirm it opens
   `https://harborfinancialapp.com`.
5. Click each contact card and confirm the intended email address and subject
   appear in the mail client.
6. Check the page once at a phone-width viewport and once on desktop.
7. In Vercel, confirm the deployment remains **Ready** and both domains remain
   **Valid Configuration**.

If Vercel asks for a domain-ownership TXT record, add only the exact TXT record
shown in the Vercel domain panel, wait for verification, and leave the existing
Google verification TXT record intact.

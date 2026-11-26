# Green Spark Existing Backup

Snapshot of the **existing live** Green Spark Solutions system, taken on 26 November 2026, before the new front end goes live.

> **This contains personal data**: client names, national ID numbers, dates of birth, phone numbers, home GPS locations and household surveys. Keep this repository **private** and limit who can access it.

## The live system that was backed up

| Part | Address | Built with | Hosted on |
|---|---|---|---|
| Public website | https://greensparksolutions.com | Next.js | DigitalOcean App Platform, behind Cloudflare |
| Admin dashboard (also the website CMS) | https://admin.greensparksolutions.com | Next.js | DigitalOcean App Platform, behind Cloudflare |
| Backend API, database and uploaded files | https://ics.greensparksolutions.com | Django REST Framework | DigitalOcean App Platform, behind Cloudflare |

## What's in this folder

| Folder | What it holds | Files | Size |
|---|---|---|---|
| `1-public-website-live-build/` | All 9 pages of the live website exactly as served, plus every JS, CSS, font and image file they load | 69 | 45.5 MB |
| `2-admin-dashboard-live-build/` | All 40 admin dashboard screens, plus every JS and CSS file | 155 | 5.4 MB |
| `3-backend-database-export/` | Every database table the backend exposes, as JSON | 1,948 | 7.9 MB |
| `4-backend-media-files/` | All 5,518 uploaded photos and PDFs (15.48 GB), in 178 zip files | 180 | 14.66 GB |

### 1 and 2: Website and admin dashboard

- `pages/` has the HTML of every route (`pages/index.html` is the home page, `pages/about-us/index.html` and so on).
- `_next/static/` has the compiled JavaScript, CSS and fonts; `public/` has the site's own images, favicon and robots.txt.
- `_crawl-log.json` lists every URL fetched and its HTTP status.
- Public pages saved: `/`, `/about-us`, `/contact-us`, `/events`, `/events/green-ics-bangladesh`, `/events/green-ics-bangladesh-part-three`, `/events/green-ics-bangladesh-part-two`, `/gallery`, `/projects`.
- The three `/events/...` pages are **not in the database**. Their text exists only in the live site's server code, so these saved pages are the only backup of that content.

### 3: Database export

Exported on 2026-11-26 through the backend API (read-only), logged in as `admin11` with the admin role.

| Table | File | Records |
|---|---|---|
| Household survey records | `survey__survey-data.json` | 1,383 |
| Cookstove clients | `api__clients.json` | 200 |
| Cookstove sales / installations | `api__sale-installation.json` | 200 |
| Survey batches | `survey__surveys.json` | 80 |
| Website: gallery images | `web__gallery-images.json` | 18 |
| Staff user accounts (no passwords) | `api__users.json` | 13 |
| Upazilas | `api__upazilas.json` | 9 |
| Regions | `api__regions.json` | 9 |
| Divisions | `api__divisions.json` | 8 |
| Region master data | `api__region-master-data.json` | 8 |
| Villages | `api__villages.json` | 7 |
| Inventory transactions | `api__inventory__transactions.json` | 5 |
| Unions | `api__unions.json` | 4 |
| Website: homepage banners | `web__banner-sliders.json` | 4 |
| Districts | `api__districts.json` | 3 |
| Products | `api__products.json` | 2 |
| Inventory stock summary | `api__inventory__summary.json` | 1 |
| Dashboard totals | `api__dashboard-summary.json` | 1 |
| Website: projects | `web__projects.json` | 1 |
| Website: gallery albums | `web__galleries.json` | 1 |
| Website: approach section | `web__approaches.json` | 1 |
| Website: vision section | `web__visions.json` | 1 |
| Website: contact details | `web__contacts.json` | 1 |
| Website: social links | `web__social-media-links.json` | 1 |
| Legacy sales table (empty, unused) | `api__sales.json` | 0 |
| Sale attachments (empty) | `api__sale-attachments.json` | 0 |
| Service monitoring (empty) | `api__sale-service-monitors.json` | 0 |
| Monitoring images (empty) | `api__sale-monitoring-images.json` | 0 |
| Client complaints (empty) | `api__client-complaints.json` | 0 |
| Monitoring forms (empty) | `api__monitoring-forms.json` | 0 |
| Website: events (empty: the live events are hard-coded in the site) | `web__events.json` | 0 |

- `<table>.json` is the full list. The `<table>/` folder next to it has one file per record, `<id>.json`, which is the detail view and can include extra nested fields.
- `_export-manifest.json` lists every endpoint, its HTTP status and record count. All returned HTTP 200.

### 4: Media files (photos and PDFs)

| Group | Zip files | Files | Size |
|---|---|---|---|
| Household survey photos | `media-survey-image-*.zip` (63) | 2,723 | 5.55 GB |
| Sale / client form PDFs | `media-forms-pdf-*.zip` (59) | 1,782 | 5.17 GB |
| Installation photos | `media-installation-*.zip` (35) | 585 | 3.01 GB |
| Client photos | `media-client-*.zip` (20) | 400 | 1.74 GB |
| Website images (banners, gallery, project, vision, products, profile) | `media-website-images-*.zip` (1) | 28 | 15.2 MB |

All 5,518 files that the database and website point to were downloaded and none were missing. Each zip is self-contained (under 100 MB for GitHub) and stores its files as `media/...`. **To restore, extract every zip into the same folder** and you get the server's `media/` directory back with identical paths. The database rows link to `https://ics.greensparksolutions.com/media/...`, so those links keep working once the folder is back in place.

Windows (PowerShell, run inside `4-backend-media-files`):

```powershell
Get-ChildItem media-*.zip | ForEach-Object { Expand-Archive $_ -DestinationPath restored -Force }
```

Mac / Linux:

```bash
for z in media-*.zip; do unzip -oq "$z" -d restored; done
```

`media-file-list.tsv` maps every file to its zip, size and SHA-256 checksum; `media-manifest.json` has the same data plus each zip's checksum.

## What this backup cannot include

These live only on the servers, so you need access to the **DigitalOcean account** (or the original developer, techtrioz.com):

1. **The original source code** of the three apps. App Platform builds each app from a Git repository or a container image, and DigitalOcean → Apps → each app → Settings → Source shows which one. Sections 1 and 2 here are the compiled, minified output the browser receives. They are good for reference and content recovery, but they are not editable source.
2. **A raw database dump.** That includes password hashes, Django's internal tables, and any table the API does not expose. To get one: DigitalOcean → Databases → the cluster → Backups, or run `pg_dump` with its connection string. The `admin11` account is not a Django staff user, so Django's own admin could not be used.
3. **Server configuration**: each app's environment variables and secrets, under App Platform → Settings.

## Checking the backup is intact

`SHA256SUMS.txt` has a checksum for every file in this folder. To check them, run this from inside the folder:

```bash
sha256sum -c SHA256SUMS.txt
```

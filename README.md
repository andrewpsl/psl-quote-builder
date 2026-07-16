# PSL Quote Builder

Quote and scope-of-work builder for Premier Sports Lighting, in the TIS-LED™ brand system.
Three scope models — **Pole In Air**, **Turnkey**, **Retrofit** — with live pricing, automatic
quote numbering, and direct PDF download.

---

## Run it

**Hosted (recommended):** enable GitHub Pages on this repo, then open
`https://<org-or-user>.github.io/psl-quote-builder/`

**Local:** download `index.html` and open it in a browser. It is fully self-contained —
the PSL logos are embedded, so there are no asset files to keep alongside it.

---

## What it does

| Feature | Notes |
|---|---|
| **Scope presets** | Pole In Air · Turnkey · Retrofit — swaps the wordmark, doc code, wedge, and both scope columns |
| **Service voltage** | 120/240 · 120/208 · 277/480 — **Turnkey only**; PIA excludes electrical, Retrofit reuses existing |
| **Live pricing** | Add/remove line items; type `285000`, it formats to `$285,000.00` |
| **Sales tax** | Rate you set, applied to line items + shipping (not bonding) |
| **Shipping / Bonding** | Optional flat amounts, added or removed per quote |
| **Exclusions note** | Rewrites itself — a quote listing sales tax never also says tax is excluded |
| **Quote number** | `PSL-MMDDYY-XYZ-NN` — date, project initials, sequence. Assigned **on download**, never on load |
| **Quote log** | Every issued quote in numerical order, with project, scope, issuer, date, total |
| **PDF download** | Writes `PSL-071626-RSC-01.pdf` straight to disk |

Everything on the sheet is editable — click any text. Every scope line can be removed and
every list can be added to, because scope changes materially per project.

---

## Storage — read this before rolling it out

The tool picks a backend automatically and **tells you which one it got** in the Quote log.

| Environment | Backend | Shared across the team? |
|---|---|---|
| Claude artifact | `window.storage` | **Yes** |
| GitHub Pages / local file | `localStorage` | **No — per browser** |
| Neither available | memory | No — clears on close |

**On GitHub Pages the quote log is per-browser.** Quotes persist across reloads on that
computer, but Andrew's log and Teresa's log are separate, and two people can issue the same
number on the same day. The log shows an orange banner saying exactly this.

### Making numbering genuinely shared

Sequencing across a team needs a server — there is no way around it in a static page.
The smallest honest version:

1. A tiny API with two routes: `GET /quotes`, `POST /quotes` (returns the assigned number)
2. Any managed Postgres, or even a single JSON file behind a lock
3. Swap the `store` adapter in `index.html` to call the API instead of `localStorage`

The adapter is isolated at the top of the script for exactly this reason — one function to
change. Real logins (verified identity, access control, an audit trail nobody can edit)
come with that same server.

---

## What "sign in" is and is not

The name field stamps **who issued a quote**. It does not verify anyone, restrict anything,
or protect the log. It is attribution, not authentication. Anyone with the URL has full access.

---

## Deploying

```bash
git clone https://github.com/<org>/psl-quote-builder.git
cd psl-quote-builder
# edit index.html
git add -A && git commit -m "Update scope wording" && git push
```

GitHub Pages redeploys on push, usually within a minute.

**Settings → Pages → Source: `main` / `root`.** Use a **private** repo — this contains
PSL scope language, payment terms, and conditions.

---

## Notes

- **Fonts** (Chakra Petch, Barlow) load from Google Fonts; the **PDF library** (html2pdf) loads
  from cdnjs. Both need internet on first load. Offline, the PDF button falls back to the
  browser print dialog and says so.
- **PDF text is rasterized**, not selectable — the cost of skipping the print dialog. The print
  dialog produces a smaller PDF with selectable text if that ever matters.
- **Structural design** references AASHTO LTS-6, matching the TIS-LED pole spec sheet.
- Scope language is transcribed from `MASTER_BID_PSL_Proposal_2026`.

© Premier Sports Lighting, LLC. Internal tool — not for distribution.

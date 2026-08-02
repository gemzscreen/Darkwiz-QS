# DARKWIZ QS

A single-file web application for Philippine quantity surveying — take-off, DUPA,
bill of quantities, cost control and progress billing.

Standards basis: **NSCP 2015 · DPWH standard pay items · RA 9184 IRR · PNS 49**

---

## Defaults

| Setting  | Default        | Switchable to        |
|----------|----------------|----------------------|
| Units    | Metric (m, m², m³, kg) | Imperial (ft, ft², ft³, lb) |
| Currency | Philippine peso (₱)    | US dollar, at an editable rate |

Both toggles sit in the toolbar and affect **display only** — every quantity is
stored in metric and every rate in peso, so switching back and forth never
rounds your data away.

---

## Deploying to GitHub Pages

1. Create a repository, e.g. `darkwiz-qs`.
2. Upload `index.html` (and this README) to the repository root.
3. **Settings → Pages → Build and deployment → Deploy from a branch**, select
   `main` and folder `/ (root)`, then save.
4. The tool goes live at `https://<your-username>.github.io/darkwiz-qs/`.

No build step, no dependencies, no server. The favicon is embedded in the file,
so nothing else needs uploading. The only external request is to Google Fonts,
and it falls back to system fonts if that is blocked.

---

## Modules

**Set-up** — project title block, unit and currency system, RA 9184 mark-up
structure, the **material database**, material coefficients, concrete mix design.

The material database groups by category, with clickable category chips below the
search bar for filtering — select several at once to compare related trades.
Category is a picker rather than free text, and typing a new one that matches an
existing category in any casing folds back into it instead of creating a
near-duplicate. New materials arrive blank at the bottom of the list, under an
**Uncategorised** heading, so nothing is silently filed in the wrong place.

**Take-off** — fifteen scope sheets, in construction sequence:

| | | |
|---|---|---|
| General preparation | Earthworks | Concreting Works |
| Formworks & scaffolding | Masonry & plaster | Roofing Works |
| Drywall and ceiling works | Tiling works | Windows, glass and doors |
| Painting works | Architectural finishes | Engineering finishes |
| Plumbing works | Electrical works | Roads & drainage |

**General preparation** covers everything before a shovel moves — mobilisation,
bonds and insurances, permits, temporary facilities, the safety programme,
signboards and final clean-up. **Roofing Works** carries both the sheets and the
framing that holds them up, so trusses, purlins and sagrods are scheduled as
mass-mode lines on the same sheet. **Architectural finishes** is now applied and
decorative work only — railings, cladding, feature panels, signage, cabinetry
and trim — while **Engineering finishes** holds the remedial and protective
items: waterproofing systems, insulation, expansion joints, epoxy grouting,
crack injection and soil treatment.

**+ New take-off sheet** at the bottom of the Take-off group creates a custom
sheet. Name it, give it a pay item range, and it behaves like any built-in sheet —
feeding the QTO, BOQ and billing automatically. Custom sheets can be deleted;
built-in ones can be renamed but not removed.

**Analysis** — quantity take-off summary, bill of materials, detailed unit price
analysis, and the rebar schedule. All are searchable, and the QTO regroups four
ways:

- consolidate by pay item
- group by take-off sheet, with sub-totals
- group by unit of measure
- line by line, no grouping

Every column heading sorts, and a source-sheet filter isolates one trade.

**Output** — bill of quantities, SAP-style cost control (budget / actual / open
commitment / assigned / available), progress billing with retention and advance
recoupment, and a portfolio view across live projects.

**Reference** — PNS 49 bar masses, **reinforcing bar grades (Grade 33, 40 and 60
with their PNS and ASTM A615 equivalents)**, DPWH concrete classes, RA 9184
mark-up ceilings, labour productivity, pakyawan rates and unit conversions.

---

## Reinforcing bar grades

| Grade | PNS 49 | f<sub>y</sub> | ASTM A615 | Min. tensile | Typical use |
|---|---|---|---|---|---|
| 33 | Grade 230 | 230 MPa | — | 390 MPa | Ties, stirrups, slab bars in ordinary work |
| 40 | Grade 275 | 275 MPa | Gr. 40 (280 MPa) | 480 MPa | General reinforcement, ordinary moment frames |
| 60 | Grade 415 | 415 MPa | Gr. 60 (420 MPa) | 620 MPa | Special moment frames and structural walls |

Unit mass is identical across grades — a 16 mm bar weighs 1.578 kg/m whichever
grade it is. Grade changes the yield strength the design relies on, and the mill
price by roughly ₱2–4 a kilo. NSCP 2015 §421 bars Grade 33 from members
resisting earthquake forces.

The rebar schedule — now under **Analysis**, after the DUPA — carries a grade per
element and reports a procurement split by grade, so the purchase order matches
the bar list.

---

## How the sheets connect

```
Material database ─┬─> Material coefficients ─> Bill of materials
                   │
DUPA library ────────> Unit bid price ─┐
                                       ├─> Bill of quantities ─> Cost control
Take-off sheets ──────> QTO summary ───┘                      └─> Progress billing
Rebar schedule ───────────┘
```

---

## Saving your work

The app saves **into a copy of itself**. Press **Save to this file** and you get a
single `.html` that is both the tool and the project — open it on any machine and
the estimate is there, with no separate data file to keep alongside it.

Files are named **Reference No. - Client/Owner - Date**, e.g.
`DARKWIZ-EST-014 - Sto. Nino Realty - 2026-08-01.html`.

### How close to "saving in place" this gets

A web page is not permitted to overwrite the file it was opened from — that
restriction is in the browser, not in this app. What each browser allows:

| Browser | Behaviour |
|---|---|
| **Chrome, Edge** | Pick a file once; every later save writes straight to it, no dialog. Effectively saving in place. |
| **Firefox, Safari** | Each save arrives as a new download. Keep the newest one. |

**Save as…** always starts a new file, whichever browser you are in.

### The browser copy

Between saves the work also sits in this browser's storage, so a refresh, a
crash, or a closed tab loses nothing. Each saved file carries its own id and gets
its own storage slot, which means two project files in the same folder never
overwrite each other's working copy.

When you open a file, whichever is newer wins — the data in the file, or the
browser copy of edits you made after it was written. In practice: open a file and
you see the file; edit and refresh and you see your edits.

The browser copy is tied to one machine, which is why the saved file is the thing
you keep and back up.

### Other formats

**Export data only (.json)** writes the project without the app — smaller, and
useful for a repository or a colleague who already has the tool. **Open** reads
both `.html` and `.json` back.

**Print / PDF** produces an A4 landscape sheet of the current module with the
title block intact. **Export CSV** writes the current module's tables for Excel.

The sidebar collapses with the **‹** button beside the logo, giving the sheet the
full window width. **☰ Modules** in the toolbar brings it back.

---

## Shared database and reporting (optional)

`report.html` is a second page that reads a Google Sheet the estimating app
publishes to, giving you portfolio, rate benchmarking, billing and material
spend across every job.

It is **one-way**: DARKWIZ publishes, the report reads. Your `.html` project
files stay the source of truth, and nothing in the Sheet writes back.

Access is via each person's own Google account, so the rate data is visible only
to whoever the spreadsheet is shared with. Setup is in **GOOGLE-SETUP.md** —
about 20 minutes, once.

Publishing requires the GitHub Pages copy, because Google will not permit
sign-in from a file opened off disk. Offline work with saved files is unaffected.

---

## Before you use this on a live tender

The seeded prices, DUPA rates and material coefficients came from the source
workbooks and from common Philippine practice. They are a starting point, not a
canvass. Replace them with current supplier quotations and your own historical
productivity before anything is submitted.

The RA 9184 mark-up ceilings shown are indicative — the governing DPWH
department order is revised periodically, so confirm the figures in force for
your procuring entity.

Prepared for PNDSYN Engineering Services.

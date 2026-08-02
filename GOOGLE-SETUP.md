# Connecting DARKWIZ QS to Google Sheets

One-time setup, about 20 minutes. Do it once and both the estimating app and
the reporting page use the same two IDs.

Because you chose **Google sign-in**, there is no Apps Script and no web-app
deployment — the browser talks to the Sheets API directly using each person's
own Google account. Access is controlled by who the spreadsheet is shared with,
so nobody outside your team can read your rates even if they find the page.

---

## Step 1 — Create the spreadsheet

1. New Google Sheet. Name it `DARKWIZ QS Database`.
2. Put it in a shared Drive folder, or share it with the people who need it.
   **Editor** for anyone publishing, **Viewer** is enough for report-only users.
3. Copy the **spreadsheet ID** out of the address bar — the long string between
   `/d/` and `/edit`:

```
https://docs.google.com/spreadsheets/d/1AbCdEf...XyZ/edit
                                        └─── this part ───┘
```

Don't create any tabs. The app does that for you in step 4.

---

## Step 2 — Google Cloud project

1. Go to <https://console.cloud.google.com> and create a project, e.g. `DARKWIZ QS`.
2. **APIs & Services → Library** → search **Google Sheets API** → **Enable**.
3. **APIs & Services → OAuth consent screen**:
   - If PNDSYN is on **Google Workspace**, choose **Internal**. This is the easy
     path — no app verification, no warning screens, and only your organisation
     can sign in.
   - On ordinary Gmail accounts you must choose **External**, leave it in
     **Testing**, and add each engineer's Gmail under **Test users**. It works
     fine; users see a "Google hasn't verified this app" screen once and click
     *Advanced → Go to DARKWIZ QS*. The 100-user cap is irrelevant at your size.
4. Add the scope `https://www.googleapis.com/auth/spreadsheets`.

---

## Step 3 — OAuth client ID

1. **APIs & Services → Credentials → Create credentials → OAuth client ID**.
2. Application type: **Web application**.
3. Under **Authorised JavaScript origins**, add your GitHub Pages address:

```
https://YOURNAME.github.io
```

**Exactly that** — scheme and host only. No trailing slash, no `/darkwiz-qs`
path. Getting this wrong is the cause of almost every sign-in error.

4. Leave **Authorised redirect URIs** empty. This flow doesn't use them.
5. Copy the **client ID**. It ends in `.apps.googleusercontent.com`.

The client ID is not a secret — it's designed to be visible in the page. There is
no client secret in this setup, which is exactly why it's safe in a static site.

---

## Step 4 — Point the apps at it

**In DARKWIZ QS** → sidebar → **Database → Publish to Sheets**:

1. Paste the client ID and spreadsheet ID.
2. **Sign in with Google**.
3. **Set up the tabs** — creates the six tabs with their headers. Run once.
4. **Test the connection** to confirm.
5. **Publish to the database**.

**In DARKWIZ QS Reports** (`report.html`) → **Connection**: paste the same two
IDs, sign in, **Load data**.

Both IDs are saved with your project file, so a file passed to a colleague
already points at the right database.

---

## Important: publishing needs the hosted copy

Google will not allow sign-in from a page opened off your hard disk — a
`file://` address can't be an authorised origin. So:

- **Publishing and reporting** happen on the GitHub Pages copy.
- **Saved `.html` project files** still work exactly as before for offline work,
  archiving and passing jobs around. They remain your source of truth.

Working pattern: estimate wherever you like, and when a job reaches a milestone,
open the hosted app, load the project file, and publish.

---

## What lands in the Sheet

Six tabs, all keyed on `Ref` + `Rev`, all money in **peso** regardless of the
display toggle:

| Tab | One row per | Feeds |
|---|---|---|
| `Projects` | project revision | Portfolio dashboard |
| `BOQ` | priced pay item | Rate benchmarking |
| `Takeoff` | measured pay item | Quantity audit |
| `Materials` | material | Material spend |
| `CostControl` | cost element | Portfolio, margin |
| `Billing` | project revision | Billing & cash position |

Re-publishing the same reference **and revision** replaces its own rows.
Publishing a **new revision** adds alongside — that's your audit trail of how an
estimate moved. Reports read the latest revision of each project, so a superseded
rev 0 never pollutes a comparison.

Columns are append-only by design. If you add columns by hand, add them at the
end, or a future version will overwrite them.

---

## Troubleshooting

| Symptom | Cause |
|---|---|
| `origin is not allowed` at sign-in | Origin in step 3 doesn't match exactly. Check for a trailing slash. |
| Sign-in window opens then closes | Popup blocker, or you're on `file://`. |
| `403` when publishing | Sheets API not enabled, or the account only has Viewer on the Sheet. |
| `404` on the spreadsheet | Wrong ID, or the Sheet isn't shared with that account. |
| Report is empty but connected | Nothing published yet, or the filters exclude everything. |
| Session expires mid-session | Tokens last about an hour. Sign in again; nothing is lost. |

---

## A limit worth knowing

Rate benchmarking compares **the rates you priced**, across jobs and revisions.
It is not actual-versus-estimated, because the app books real cost by element —
materials, labour, equipment, subcontracts — not against each pay item. Closing
that loop means capturing site cost per pay item, which is a change to how cost
is recorded rather than another report. Worth discussing if that comparison is
what you're really after.

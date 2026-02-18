# Street Training Tools

Internal tooling for the Street Product Training team. Hosted via GitHub Pages.

## Pages

| File | Description | URL |
|------|-------------|-----|
| `dashboard.html` | Trainer CSAT dashboard — pulls live feedback from Google Sheets | `/dashboard.html` |
| `til-tracker.html` | Time in Lieu tracker — logs earned and taken hours per trainer | `/til-tracker.html` |
| `survey.html` | Post-training feedback survey — submits to Google Sheets via Apps Script | `/survey.html` |
| `training-request.html` | Client training request form — submits to Google Sheets via Apps Script | `/training-request.html` |

## Navigation order (consistent across all pages)
1. Dashboard
2. TiL Tracker
3. Training Survey
4. Training Request
5. Training Menu (links to Google Drive PDF ↗)

---

## Setup

### GitHub Pages
1. Go to **Settings → Pages**
2. Set source to **Deploy from a branch → `main` → `/root`**
3. Your base URL will be: `https://YOUR-USERNAME.github.io/YOUR-REPO-NAME/`

### Dashboard — Google Sheets API
The dashboard reads survey responses from Google Sheets using the Sheets API.

In `dashboard.html`, update these constants near the bottom:
```js
const SHEET_ID = "your-sheet-id-here";       // from your Google Sheet URL
const API_KEY  = "your-api-key-here";         // from Google Cloud Console
```

Make sure your sheet is set to **Anyone with the link can view**.

---

### Survey — Apps Script (already connected)
The post-training survey posts to Google Sheets via an Apps Script Web App.  
The URL is already set in `survey.html`. No changes needed unless you redeploy.

---

### Training Request Form — Apps Script setup

The training request form needs its own Apps Script deployment.

**Step 1 — Add the script to your Google Sheet**

1. Open your Google Spreadsheet
2. Go to **Extensions → Apps Script**
3. Paste the following code:

```js
function doPost(e) {
  const data  = JSON.parse(e.parameter.payload);
  const ss    = SpreadsheetApp.getActiveSpreadsheet();
  const sheet = ss.getSheetByName("Training Form Response");

  // Add header row if sheet is empty
  if (sheet.getLastRow() === 0) {
    sheet.appendRow([
      "Timestamp", "Email", "Company / Business Name",
      "Authorising Stakeholder", "Training Package",
      "Attendees", "Team Familiarity", "Modules to Cover",
      "Top 3 Priorities", "Specific Features / Workflows",
      "Current Challenges", "Training Location",
      "Target Go-Live Date", "Post-Training Support", "Anything Else"
    ]);
  }

  sheet.appendRow([
    data.timestamp,
    data.email,
    data.company,
    data.stakeholder,
    data.package,
    data.attendees,
    data.familiarity,
    data.modules,
    data.priorities,
    data.specificFeatures,
    data.challenges,
    data.location,
    data.goLiveDate,
    data.support,
    data.anythingElse
  ]);

  return ContentService.createTextOutput("ok");
}
```

**Step 2 — Deploy as a Web App**

1. Click **Deploy → New Deployment**
2. Type: **Web App**
3. Execute as: **Me**
4. Who has access: **Anyone**
5. Click **Deploy** and copy the URL

**Step 3 — Add the URL to the form**

In `training-request.html`, find this line near the bottom:
```js
const TRAINING_REQUEST_APPS_SCRIPT_URL = "YOUR_APPS_SCRIPT_WEB_APP_URL_HERE";
```
Replace `YOUR_APPS_SCRIPT_WEB_APP_URL_HERE` with the URL you copied.

**Step 4 — Make sure the tab exists**

In your Google Sheet, ensure there is a tab named exactly:
```
Training Form Response
```

---

## Updating files

Since all pages are in one repo with relative links, any update you push will reflect across the whole site. To update:

```bash
git add .
git commit -m "your update message"
git push
```

GitHub Pages usually reflects changes within 1–2 minutes.

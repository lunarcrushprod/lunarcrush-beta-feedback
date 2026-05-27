# Google Sheet + Apps Script Setup

This connects the feedback form to a Google Sheet. About 5 minutes to set up.

## Step 1: Create the Sheet

1. Go to https://sheets.new
2. Name it: **LunarCrush Beta Feedback**
3. In row 1, paste these headers across columns A–L:

```
Timestamp	Name	Email	Role	Device	Overall Rating	What Worked	What Broke	Missing Features	Compared to Current Site	Would Recommend	NPS
```

## Step 2: Add the Apps Script

1. In the sheet: **Extensions → Apps Script**
2. Delete the default `function myFunction()` block
3. Paste this:

```javascript
function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    const data = JSON.parse(e.postData.contents);

    sheet.appendRow([
      new Date(),
      data.name || '',
      data.email || '',
      data.role || '',
      data.device || '',
      data.rating || '',
      data.worked || '',
      data.broke || '',
      data.missing || '',
      data.comparison || '',
      data.recommend || '',
      data.nps || ''
    ]);

    return ContentService
      .createTextOutput(JSON.stringify({ status: 'success' }))
      .setMimeType(ContentService.MimeType.JSON);
  } catch (err) {
    return ContentService
      .createTextOutput(JSON.stringify({ status: 'error', message: err.toString() }))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet() {
  return ContentService.createTextOutput('LunarCrush feedback endpoint is live.');
}
```

4. Click the **Save** icon (or Cmd+S)
5. Name the project: **LunarCrush Beta Feedback Handler**

## Step 3: Deploy

1. Click **Deploy → New deployment**
2. Click the gear icon next to "Select type" → choose **Web app**
3. Configure:
   - **Description**: `Beta feedback v1`
   - **Execute as**: `Me`
   - **Who has access**: `Anyone` (this is required for the form to submit without auth — the script only writes to your sheet)
4. Click **Deploy**
5. Authorize when prompted (it'll warn about "unverified app" — click Advanced → Go to project → Allow. This is normal for personal Apps Scripts.)
6. Copy the **Web app URL** — it looks like `https://script.google.com/macros/s/AKfyc.../exec`

## Step 4: Paste the URL into the form

In the HTML form artifact, find this line near the top of the `<script>` block:

```javascript
const SCRIPT_URL = 'PASTE_YOUR_APPS_SCRIPT_URL_HERE';
```

Replace the placeholder with your URL. That's it.

## Test it

Open the form, submit a test entry, check the sheet. Row should appear within a second or two.

## Debugging note

The form uses `mode: 'no-cors'` to submit (Apps Script doesn't support CORS preflight on web apps). This means: **the form will always show "Success" to the user, even if the write actually failed.** Always do an end-to-end test yourself before sending the email out.

If submissions aren't appearing in the sheet:
1. Open Apps Script → **Executions** (left sidebar) to see actual error logs from `doPost`
2. Make sure the deployment access is set to **Anyone** (not "Anyone with Google account")
3. If you redeployed, the URL stays the same only if you use **Manage deployments → Edit → New version**. A fresh **New deployment** gives a new URL.

## Why this approach

- No backend to host or pay for
- No API keys to manage or rotate
- The sheet is the database; query, filter, pivot natively
- Free, durable, easy to hand off to anyone else on the team

## If you want to get fancy later

- Add a column for `User Agent` (capture from `navigator.userAgent` in the form, send as `data.userAgent`)
- Trigger a Slack notification on each submit via the Apps Script (add a `UrlFetchApp.fetch()` call to a Slack webhook inside `doPost`)
- Pipe the sheet into Looker Studio for a live dashboard

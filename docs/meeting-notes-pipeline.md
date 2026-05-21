# Meeting Notes → Tickets Pipeline

Automate the flow from meeting notes to development tickets. A Google Apps Script watches your meeting notes doc, detects when a meeting ends (15 min inactivity), and posts new notes to Slack for review and ticket creation.

## Architecture

```
Meeting ends (you stop typing)
 ↓ (15 min inactivity detected)
Google Apps Script reads new section
 ↓
Posts to Slack channel for review
 ↓
You approve proposed tickets
 ↓
Tickets created in Linear/GitHub
 ↓
Dev workflow picks them up
```

## Prerequisites

- A Google Doc where you keep meeting notes (dated sections)
- A Slack workspace with a bot app (Socket Mode enabled)
- Incoming Webhooks enabled on the Slack app

## Setup

### Step 1 — Enable Incoming Webhooks on your Slack app

1. Go to https://api.slack.com/apps → select your app
2. Left sidebar → **Incoming Webhooks** → toggle On
3. Click **Add New Webhook to Workspace**
4. Pick or create a dedicated channel (e.g. `#meeting-actions`)
5. Copy the webhook URL
6. If prompted, reinstall the app to apply new permissions

### Step 2 — Create the Apps Script

1. Open your Google Doc → *Extensions → Apps Script*
2. Rename the project (e.g. `Meeting Notes Watcher`)
3. Replace the default code with:

```javascript
function checkForInactivity() {
  var doc = DocumentApp.getActiveDocument();
  var props = PropertiesService.getScriptProperties();
  var lastContent = props.getProperty('lastContent') || '';
  var lastChangeTime = props.getProperty('lastChangeTime') || '0';
  var currentContent = doc.getBody().getText();
  var now = new Date().getTime();

  if (currentContent !== lastContent) {
    props.setProperty('lastContent', currentContent);
    props.setProperty('lastChangeTime', String(now));
    return;
  }

  var elapsed = now - parseInt(lastChangeTime);
  var fifteenMin = 15 * 60 * 1000;
  var processed = props.getProperty('lastProcessedTime') || '0';

  if (elapsed >= fifteenMin && lastChangeTime !== '0' && lastChangeTime !== processed) {
    props.setProperty('lastProcessedTime', lastChangeTime);
    sendToSlack(extractNewSection(currentContent, props));
  }
}

function extractNewSection(content, props) {
  var lastProcessed = props.getProperty('lastProcessedContent') || '';
  props.setProperty('lastProcessedContent', content);

  if (!lastProcessed) return content.slice(-3000);

  var idx = content.indexOf(lastProcessed.slice(-500));
  if (idx === -1) return content.slice(-3000);
  return content.slice(idx + 500);
}

function sendToSlack(newText) {
  if (!newText || newText.trim().length < 50) return;

  var webhook = 'YOUR_SLACK_WEBHOOK_URL_HERE';
  var payload = {
    text: '📋 *New meeting notes detected* (15 min inactivity)\n\n' +
      newText.substring(0, 3000) +
      '\n\n_Reply with proposed Linear tickets for approval._'
  };

  UrlFetchApp.fetch(webhook, {
    method: 'post',
    contentType: 'application/json',
    payload: JSON.stringify(payload)
  });
}
```

4. Replace `YOUR_SLACK_WEBHOOK_URL_HERE` with your actual webhook URL
5. Save (Ctrl+S)

### Step 3 — Set up the timer trigger

1. In Apps Script, click the clock icon (⏰) in the left sidebar
2. Click **+ Add Trigger**
3. Settings:
   - Function: `checkForInactivity`
   - Deployment: `Head`
   - Event source: `Time-driven`
   - Type: `Minutes timer`
   - Interval: `Every 15 minutes`
4. Click Save and authorize when prompted

### Step 4 — Test

1. Add a line to your Google Doc
2. Wait 15 minutes
3. Check your Slack channel — new notes should appear

## How it works

- The script runs every 15 minutes and compares the doc content with its last snapshot
- If the content changed: it updates the snapshot and timestamp (you're still in a meeting)
- If the content is the same AND 15+ minutes passed since the last change: meeting is over, extract new section and post to Slack
- Only new content (since last processing) is sent — not the entire doc

## Phase 2: AI-powered ticket extraction (future)

Connect the Slack output to an AI model (e.g. Claude on Bedrock) that:

1. Reads the new meeting notes
2. Extracts action items, decisions, and proposed tickets
3. Posts structured ticket proposals to Slack with Approve/Skip buttons
4. Creates approved tickets in Linear automatically
5. Dev workflow agent picks them up

## Customization

- Change the inactivity threshold by modifying the `fifteenMin` value
- Change the Slack message format in `sendToSlack`
- Adjust `content.slice(-3000)` to capture more or less text
- Add multiple webhooks to notify different channels

## Troubleshooting

- **No message in Slack**: Check that the webhook URL is correct, the app is reinstalled, and the trigger is active (clock icon → check for errors)
- **Message contains old content**: The script compares against its last snapshot. Delete script properties (File → Project Properties → Script Properties → delete all) to reset
- **Too many notifications**: The script only fires once per "inactivity window" — it won't re-send until new content is added and another 15 min passes

# Auto Support Drafter

**Auto Support Drafter** is a Google Apps Script that automatically scans your Gmail inbox for customer support emails related to **refunds** or **bugs**, and drafts tailored email responses. It uses **regex matching**, time-based filtering, and Gmail labels to automate up to 90% of your frontline support triage — all while allowing full manual review before sending.

---

## ✨ Features

- **Regex-powered filtering** for refund- and bug-related messages
- **Auto-drafts responses** with dynamic HTML content
- **Avoids duplicate replies** and detects team replies
- **Labels processed threads** with `"AUTO DRAFT SUPPORT"`
- **Sends internal summary emails** to your support team
- **Customizable global variables** for names, emails, brand name, and refund code

---

## 🧠 How It Works

### 1. **Regex Matching**
The script scans the latest 20 threads from the inbox (within the last hour) using Gmail's search API:

```js
GmailApp.search(
  `in:inbox (Refund OR Bug OR Issue OR Cancel OR Refunding OR Cancellation) after:${gmailDate}`
)
````

Then uses **JavaScript regular expressions** to detect keywords:

```js
const refundRegex = /\b(refund|cancel|cancellation|refunding|subscription)\b/i;
const bugRegex = /\b(bug|issue|error|glitch|problem)\b/i;
```

These patterns match **subject lines and message bodies** to determine if the thread should receive a refund or bug response.

### 2. **Filters Out:**

* Messages **older than 1 hour**
* Messages where **your team has already replied**
* Messages **not sent by a customer**

### 3. **Draft Creation**

If a match is found and it's valid:

* A **custom HTML response** is drafted
* The thread is labeled `"AUTO DRAFT SUPPORT"`
* Your team is **CC'd** on the draft

### 4. **Internal Summary Email**

At the end of the script (between 10am–5pm EST), a summary email is sent with:

* Total refund messages drafted
* Total bug reports responded to
* A link to Gmail drafts for review

---

## ⚙️ Setup Instructions

### Step 1: Open Google Apps Script

1. Go to [script.google.com](https://script.google.com)
2. Click **New Project**
3. Paste the contents of `auto-support-drafter.gs` into `Code.gs`

---

### Step 2: Configure Global Variables

At the top of the script, configure these:

```javascript
const APP_NAME = "Sizzle";
const SUPPORT_TEAM_NAMES = ["Lily", "Dilan", "Alexa", "Benjamin"];
const SUPPORT_TEAM_EMAILS = [
  "alexa@joinsizzle.app",
  "dilan@joinsizzle.app",
  "benjamin@joinsizzle.app"
];
const CC_EMAIL = "benjamin@joinsizzle.app";
const REFUND_CODE = "528511";
```

You can change `APP_NAME`, `CC_EMAIL`, or add more team members as needed.

---

### Step 3: Grant Permissions

* Click the ▶️ run button inside Apps Script to **run `autoDraftSupportReplies()` once**.
* You’ll be prompted to **grant permissions** to access Gmail and send emails.

---

### Step 4: Set Up Time-Based Trigger (Optional)

To automate it hourly:

1. Click on the clock icon (⏰ "Triggers")
2. Add a new trigger:

   * **Function**: `autoDraftSupportReplies`
   * **Event Source**: Time-driven
   * **Type**: Hourly (every 1 hour)
3. Save.

---

## ✏️ Customization

| Option              | How to Use                                          |
| ------------------- | --------------------------------------------------- |
| Change brand name   | Set `APP_NAME = "YourBrand"`                        |
| Adjust keywords     | Modify `refundRegex` and `bugRegex` patterns        |
| Support hours (EST) | Change the range in `if (shouldSend)` check         |
| Update team members | Edit `SUPPORT_TEAM_NAMES` and `SUPPORT_TEAM_EMAILS` |
| Modify responses    | Edit the `refundResponse` and `bugResponse` strings |

---

## 🛠 Requirements

* A Gmail account with access to Google Apps Script
* Authorized script access to Gmail
* Basic understanding of Google Workspace

---

## ❗ Disclaimer

This tool is designed for **internal support automation**. By default, it **does not auto-send emails**, but rather **creates drafts** for your team to review and send manually.

However, **you can enable auto-sending** by modifying the `thread.createDraftReply()` line to `thread.reply()` in the script. This will cause the message to be **immediately sent to the user** once detected.

### ⚠️ How to Enable Auto-Send:

1. Locate this line in the script:

   ```javascript
   thread.createDraftReply(draftBody, {
     cc: CC_EMAIL,
     htmlBody: draftBody
   });
   ```

2. Replace it with:

   ```javascript
   thread.reply(draftBody, {
     cc: CC_EMAIL,
     htmlBody: draftBody
   });
   ```

3. Save and re-authorize the script if prompted.

> ⚠️ **Important:** Use this only if you're confident in the message quality and logic, as replies will be sent immediately without human review.

Use responsibly and only in accounts where you have permission.

---

## 📬 Support or Contributions

If you'd like to improve the script, file an issue or open a pull request.

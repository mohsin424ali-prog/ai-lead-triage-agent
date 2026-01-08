# 🚀 Complete Setup Guide

This guide will walk you through deploying the AI Lead Triage Agent from scratch.

## ⏱️ Estimated Time: 30 minutes

---

## 📋 Prerequisites

Before you begin, make sure you have:

- [ ] GitHub account
- [ ] Railway account (sign up at [railway.app](https://railway.app))
- [ ] Google account (for Sheets & Gmail)
- [ ] Google Cloud project (free tier is fine)

---

## 🎯 Step 1: Fork & Clone Repository

### 1.1 Fork the Repository

1. Go to the GitHub repository
2. Click "Fork" button (top right)
3. Select your account

### 1.2 Clone to Your Machine

```bash
git clone https://github.com/YOUR-USERNAME/ai-lead-triage-agent.git
cd ai-lead-triage-agent
```

---

## ☁️ Step 2: Deploy to Railway

### 2.1 Create New Project

1. Go to [railway.app/dashboard](https://railway.app/dashboard)
2. Click "New Project"
3. Select "Deploy from GitHub"
4. Choose your forked repository

### 2.2 Railway Auto-Detection

Railway will automatically:
- ✅ Detect n8n as the application
- ✅ Create PostgreSQL database
- ✅ Generate a public URL
- ✅ Enable HTTPS/SSL

Wait 2-3 minutes for deployment to complete.

### 2.3 Get Your Railway URL

1. Click on your n8n service
2. Go to "Settings" tab
3. Find "Domains" section
4. Copy your URL: `https://your-project-name.up.railway.app`

---

## ⚙️ Step 3: Configure Environment Variables

### 3.1 Access Variables Tab

1. In Railway dashboard
2. Click on your n8n service
3. Go to "Variables" tab

### 3.2 Add Required Variables

Add these one by one:

```bash
# CRITICAL - Update with your actual Railway URL
WEBHOOK_URL=https://your-project-name.up.railway.app/

# Security
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=YourSecurePassword123!

# Generate random 32-character string
N8N_ENCRYPTION_KEY=abcdef1234567890abcdef1234567890

# Host configuration
N8N_HOST=your-project-name.up.railway.app
N8N_PROTOCOL=https
N8N_PORT=5678

# Timezone
GENERIC_TIMEZONE=Asia/Karachi
TZ=Asia/Karachi
```

### 3.3 Restart Service

1. Click "Deploy" dropdown
2. Select "Restart"
3. Wait 30 seconds

---

## 🔐 Step 4: Set Up Google Cloud Credentials

### 4.1 Create Google Cloud Project

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Click "Select Project" → "New Project"
3. Name it: "n8n Lead Automation"
4. Click "Create"

### 4.2 Enable APIs

1. Go to "APIs & Services" → "Library"
2. Search and enable:
   - **Google Sheets API**
   - **Gmail API**
   - **Google Drive API** (optional but recommended)

### 4.3 Create OAuth2 Credentials

#### For Google Sheets:

1. Go to "APIs & Services" → "Credentials"
2. Click "Create Credentials" → "OAuth client ID"
3. Configure consent screen (if prompted):
   - User Type: External
   - App name: "n8n Lead Automation"
   - Support email: your email
   - Scopes: Add `.../auth/spreadsheets`
4. Application type: "Web application"
5. Name: "n8n Google Sheets"
6. Authorized redirect URIs:
   ```
   https://your-project-name.up.railway.app/rest/oauth2-credential/callback
   ```
7. Click "Create"
8. **Save Client ID and Client Secret**

#### For Gmail:

1. Click "Create Credentials" again
2. OAuth client ID → Web application
3. Name: "n8n Gmail"
4. Same redirect URI as above
5. **Save Client ID and Client Secret**

### 4.4 Get Google Gemini API Key

1. Go to [ai.google.dev](https://ai.google.dev)
2. Click "Get API Key"
3. Create new API key
4. **Save the API key**

---

## 📊 Step 5: Create Google Sheet

### 5.1 Create New Sheet

1. Go to [sheets.google.com](https://sheets.google.com)
2. Click "+ Blank"
3. Name it: "Lead Tracker"

### 5.2 Add Column Headers

In row 1, add these headers (exact spelling):

```
Lead ID | Company Name | Contact Name | Email | Website | Industry | 
Company Size | ICP Score | Status | Draft Subject | Draft Body | 
Reasoning | Pain Points | Automation Opportunities | 
Submission Time | Processed Time
```

### 5.3 Get Sheet ID

From the URL:
```
https://docs.google.com/spreadsheets/d/SHEET_ID_HERE/edit
```

Copy the `SHEET_ID_HERE` part.

### 5.4 Share with Your Google Account

1. Click "Share" button
2. Add your email
3. Give "Editor" permissions

---

## 🔧 Step 6: Configure n8n Workflow

### 6.1 Access Your n8n Instance

1. Open your Railway URL: `https://your-project-name.up.railway.app`
2. Login with credentials from environment variables:
   - Username: `admin`
   - Password: (what you set)

### 6.2 Import Workflow

1. Click "Workflows" in left sidebar
2. Click "+ Add workflow" → "Import from File"
3. Upload: `workflows/ai-lead-triage-agent-v2.json`
4. Click "Import"

### 6.3 Configure Google Sheets Credential

1. Click on any "Google Sheets" node (red icon)
2. Click "Credential to connect with"
3. Click "+ Create New"
4. Select "OAuth2 API"
5. Fill in:
   - **Name**: "Google Sheets account"
   - **Client ID**: (from Step 4.3)
   - **Client Secret**: (from Step 4.3)
6. Click "Connect my account"
7. Follow Google OAuth flow
8. Click "Save"

### 6.4 Configure Gmail Credential

1. Click on any "Gmail" node
2. Create new credential
3. OAuth2 API
4. Fill in Client ID & Secret
5. Connect and authorize
6. Save

### 6.5 Configure Google Gemini Credential

1. Click on "Google Gemini Chat Model" node
2. Create new credential
3. Enter API key from Step 4.4
4. Save

### 6.6 Update Google Sheet ID

1. Click on any "Google Sheets" node
2. In "Document" field
3. Click to select
4. Paste your Sheet ID
5. Select your sheet name
6. Repeat for all Google Sheets nodes (3 total)

### 6.7 Update Email Address

1. Click "Notify SDR - High Value" node
2. Update "Send To" field with your email
3. Repeat for "Notify SDR - Medium Value" node

---

## ✅ Step 7: Activate Workflow

### 7.1 Toggle Workflow Active

1. In workflow editor
2. Look for toggle switch (top right)
3. Click to turn **ON** (should be green)
4. Status should show: 🟢 Active

### 7.2 Verify Webhook URL

1. Click on "Webhook - Lead Intake" node
2. Check "Production URL":
   ```
   https://your-project-name.up.railway.app/webhook/lead-intake
   ```
3. This is your API endpoint!

---

## 🧪 Step 8: Test Your Deployment

### 8.1 Basic Webhook Test

Open terminal and run:

```bash
curl -X POST https://your-project-name.up.railway.app/webhook/lead-intake \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Test User",
    "email": "test@stripe.com",
    "company": "Stripe",
    "website": "https://stripe.com"
  }'
```

### 8.2 Expected Response

```json
{
  "status": "success",
  "message": "Thank you for your interest!...",
  "lead_id": "test_1234567890",
  "lead_score": 85,
  "company": "Stripe"
}
```

### 8.3 Check Your Email

Within 30 seconds, you should receive:
- Subject: "🔥 HIGH-VALUE LEAD: Stripe (85/100)"
- Contains all lead details
- Has "APPROVE & SEND EMAIL" button

### 8.4 Test Approval Link

1. Click the button in the email
2. Should see: `{"message": "Execution resumed successfully"}`
3. Check Gmail again for prospect email
4. Check Google Sheets for new row

---

## ✅ Verification Checklist

Before going live, verify:

- [ ] Workflow is active (green toggle)
- [ ] Webhook responds (200 OK)
- [ ] Google Sheets credential works
- [ ] Gmail credential works
- [ ] Gemini API key works
- [ ] Email validation rejects @gmail.com
- [ ] Duplicate detection works
- [ ] High-value leads trigger approval
- [ ] Approval link works from email
- [ ] Emails sent to prospects
- [ ] All data logged to Sheets
- [ ] Medium/Low leads processed correctly

---

## 🐛 Troubleshooting

### Issue: 404 Not Found

**Cause:** Workflow not active or wrong URL

**Fix:**
```bash
1. Check workflow toggle is GREEN
2. Verify WEBHOOK_URL environment variable
3. Restart Railway service
```

### Issue: Credentials Not Working

**Cause:** OAuth not configured correctly

**Fix:**
```bash
1. Check redirect URI is exact
2. Re-authenticate each credential
3. Ensure APIs are enabled in Google Cloud
```

### Issue: No Email Received

**Cause:** Gmail credential or email address issue

**Fix:**
```bash
1. Check spam/junk folder
2. Verify email address in nodes
3. Re-authenticate Gmail OAuth
4. Check Gmail API quota
```

### Issue: Approval Link 404

**Cause:** WEBHOOK_URL not set correctly

**Fix:**
```bash
1. Add trailing slash: https://domain.com/
2. Restart Railway service
3. Test new lead submission
```

### Issue: Google Sheets Not Logging

**Cause:** Sheet ID or credentials issue

**Fix:**
```bash
1. Verify Sheet ID is correct
2. Check sheet has "Email" column
3. Re-authenticate Google Sheets
4. Check sheet permissions
```

---

## 📝 Post-Setup Tasks

### 1. Update README with Your URLs

Replace placeholder URLs with your actual Railway URL:
```bash
# In README.md
https://your-project-name.up.railway.app
```

### 2. Add Screenshot to Repository

Take screenshots of:
- Workflow in n8n
- Sample approval email
- Google Sheets with data
- Save in `/assets` folder

### 3. Test All Scenarios

Run through complete testing checklist:
- Personal email rejection
- Duplicate detection
- High/Medium/Low value leads
- Error handling

### 4. Monitor for 24 Hours

Check:
- Railway logs for errors
- n8n executions for failures
- Google Sheets for data quality
- Email delivery rates

---

## 🎉 You're Ready!

Your AI Lead Triage Agent is now live and ready to process leads!

### Next Steps:

1. 📢 Share your webhook URL with form submissions
2. 📊 Monitor Google Sheets for incoming leads
3. 📧 Review and approve high-value leads
4. 🔧 Customize scoring criteria as needed
5. 📈 Track conversion rates

---

## 💡 Pro Tips

1. **Set up monitoring**: Add Slack notifications for high-value leads
2. **Archive old data**: Move leads older than 90 days to separate sheet
3. **Backup regularly**: Export Google Sheets weekly
4. **Optimize scoring**: Adjust ICP criteria based on actual conversions
5. **A/B test emails**: Try different subject lines and track results

---

## 📞 Need Help?

- 📖 [Full Documentation](../README.md)
- 🐛 [Report Issues](https://github.com/yourusername/ai-lead-triage-agent/issues)
- 💬 [Ask Questions](https://github.com/yourusername/ai-lead-triage-agent/discussions)
- 📧 Email: mohsin424ali@gmail.com

---

**Congratulations! Your setup is complete!** 🎊

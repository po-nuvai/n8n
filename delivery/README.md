# Food Delivery WhatsApp Notification System

Automated WhatsApp notification system for food delivery businesses. Send real-time order status updates to customers via WhatsApp Business API using n8n workflow automation.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [What You'll Need](#what-youll-need)
- [Quick Start](#quick-start)
- [Detailed Setup Guide](#detailed-setup-guide)
  - [Step 1: WhatsApp Business API Setup](#step-1-whatsapp-business-api-setup)
  - [Step 2: Create Message Templates](#step-2-create-message-templates)
  - [Step 3: Install n8n](#step-3-install-n8n)
  - [Step 4: Configure Environment Variables](#step-4-configure-environment-variables)
  - [Step 5: Import Workflow](#step-5-import-workflow)
  - [Step 6: Test the System](#step-6-test-the-system)
  - [Step 7: Integrate with Your App](#step-7-integrate-with-your-app)
- [API Keys Configuration](#api-keys-configuration)
- [Webhook Integration](#webhook-integration)
- [Testing](#testing)
- [Troubleshooting](#troubleshooting)
- [Production Deployment](#production-deployment)
- [FAQ](#faq)
- [File Structure](#file-structure)
- [Support](#support)
- [License](#license)

---

## Overview

This system automatically sends WhatsApp messages to your customers when their order status changes. It integrates seamlessly with any food delivery platform and uses WhatsApp's official Business API.

### How It Works

```
Customer Orders → Your App → n8n Webhook → WhatsApp API → Customer's Phone
```

1. Customer places an order in your food delivery app
2. Your app calls the n8n webhook when order status changes
3. n8n workflow routes the message based on status
4. WhatsApp API sends the appropriate template message
5. Customer receives instant notification on WhatsApp

### Message Types

**1. Order Received** - Confirms order placement
```
Hi John, we've received your order #12345
for 2x Pizza, 1x Coke. We're preparing it now!

Thank you for choosing us!
```

**2. In Process** - Kitchen preparation update
```
Hi John, your order #12345 is being prepared
by our kitchen!

Estimated time: 25 minutes.

We'll notify you when it's ready for dispatch!
```

**3. Dispatched** - Delivery driver details
```
Hi John, your order #12345 is on its way!

Driver: Mike Smith
Contact: 9876543210

Track your order: [tracking link]

Enjoy your meal!
```

---

## Features

- ✅ **Fully Automated** - No manual intervention required
- 📱 **Official WhatsApp Business API** - Reliable and compliant
- 🔄 **Real-time Updates** - Instant notifications
- 🌐 **Platform Agnostic** - Works with PHP, Node.js, Python, Java, etc.
- 📊 **Easy Monitoring** - Track all messages from n8n dashboard
- 🔒 **Secure** - Uses environment variables for credentials
- 🎨 **Customizable** - Modify templates to match your brand
- 📈 **Scalable** - Handles from 10 to 100,000+ orders per day

---

## What You'll Need

### Required Services

1. **WhatsApp Business API Account** (from Meta/Facebook)
   - Free to set up
   - Requires business verification
   - Get at: https://business.facebook.com

2. **n8n Platform** (Workflow Automation)
   - Free plan available
   - Cloud or self-hosted options
   - Get at: https://n8n.io

3. **Your Food Delivery System**
   - Any platform that can make HTTP requests
   - PHP, Node.js, Python, Java, etc.

### Estimated Costs

- **WhatsApp Business API**: First 1,000 conversations/month often free, then varies by country
- **n8n Cloud**: Free plan available, paid plans from $20/month
- **n8n Self-hosted**: Free (just server costs)

**Total: Can start with $0-20/month**

---

## Quick Start

**Total setup time: 2-3 hours (including template approval wait)**

1. ✅ Create WhatsApp Business API account (30-60 min)
2. ✅ Create 3 message templates (15-30 min)
3. ✅ Wait for Meta approval (15 min - 24 hours)
4. ✅ Set up n8n and import workflow (15 min)
5. ✅ Configure API keys (5 min)
6. ✅ Test the system (10 min)
7. ✅ Integrate with your app (15-30 min)
8. ✅ Go live!

---

## Detailed Setup Guide

### Step 1: WhatsApp Business API Setup

#### 1.1 Create Meta Business Account

1. Go to https://business.facebook.com
2. Click **"Create Account"**
3. Fill in your business details
4. Complete verification (may require business documents)

#### 1.2 Set Up WhatsApp Business API

1. In Meta Business Suite, click **"WhatsApp"**
2. Click **"Get Started"**
3. Select **"Use WhatsApp Business API"**
4. Follow the verification process
5. Add a phone number for your business

#### 1.3 Get Your Credentials

**Phone Number ID:**
1. Go to Meta Business Manager → WhatsApp Manager
2. Click **"Phone Numbers"**
3. Copy the **Phone Number ID** (e.g., `102290129340398`)
4. Save this - you'll need it later

**Access Token:**
1. Go to Meta Business Manager → **System Users**
2. Click **"Add"** to create a system user
3. Give permissions: WhatsApp Business Management + Messaging
4. Click **"Generate New Token"**
5. Select your WhatsApp Business app
6. Copy the **Permanent Access Token**
7. Save this securely - you won't see it again!

---

### Step 2: Create Message Templates

WhatsApp requires pre-approved templates for business messages. Create these 3 templates:

#### Template 1: order_received

1. Go to Meta Business Manager → WhatsApp Manager → **Message Templates**
2. Click **"Create Template"**
3. Fill in:
   - **Name**: `order_received`
   - **Category**: **TRANSACTIONAL** (Important!)
   - **Language**: English
   - **Body**:
     ```
     Hi {{1}}, we've received your order #{{2}} for {{3}}. We're preparing it now! 🎉

     Thank you for choosing us!
     ```
4. Click **"Submit"**
5. Wait for approval

#### Template 2: order_in_process

1. Create another template
2. Fill in:
   - **Name**: `order_in_process`
   - **Category**: **TRANSACTIONAL**
   - **Language**: English
   - **Body**:
     ```
     Hi {{1}}, your order #{{2}} is being prepared by our kitchen! 👨‍🍳

     Estimated time: {{3}} minutes.

     We'll notify you when it's ready for dispatch!
     ```
3. Click **"Submit"**

#### Template 3: order_dispatched

1. Create another template
2. Fill in:
   - **Name**: `order_dispatched`
   - **Category**: **TRANSACTIONAL**
   - **Language**: English
   - **Body**:
     ```
     Hi {{1}}, your order #{{2}} is on its way! 🚀

     Driver: {{3}}
     Contact: {{4}}

     Track your order: {{5}}

     Enjoy your meal!
     ```
3. Click **"Submit"**

**Wait for all 3 templates to be approved** (usually 15 minutes - 24 hours)

---

### Step 3: Install n8n

Choose one installation method:

#### Option A: n8n Cloud (Easiest - Recommended)

1. Go to https://n8n.io
2. Sign up for an account
3. Choose a plan (free plan available)
4. Your n8n URL: `https://[your-account].app.n8n.cloud`

#### Option B: Self-Hosted

```bash
# Install n8n globally
npm install n8n -g

# Start n8n
n8n start

# Access at: http://localhost:5678
```

#### Option C: Docker

```bash
docker run -d \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

---

### Step 4: Configure Environment Variables

Add your WhatsApp credentials to n8n.

#### For n8n Cloud:

1. Log in to your n8n account
2. Go to **Settings** → **Environment Variables**
3. Click **"Add Variable"**
4. Add first variable:
   - **Name**: `WHATSAPP_PHONE_NUMBER_ID`
   - **Value**: [Your Phone Number ID]
5. Add second variable:
   - **Name**: `WHATSAPP_ACCESS_TOKEN`
   - **Value**: [Your Access Token]
6. Click **"Save"**

#### For Self-Hosted n8n:

1. Create or edit: `~/.n8n/.env`
2. Add these lines:
   ```bash
   WHATSAPP_PHONE_NUMBER_ID=your_phone_number_id_here
   WHATSAPP_ACCESS_TOKEN=your_access_token_here
   ```
3. Restart n8n:
   ```bash
   # Stop n8n (Ctrl+C)
   # Start again
   n8n start
   ```

#### For Docker n8n:

```bash
docker run -d \
  --name n8n \
  -p 5678:5678 \
  -e WHATSAPP_PHONE_NUMBER_ID="your_phone_number_id" \
  -e WHATSAPP_ACCESS_TOKEN="your_access_token" \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

---

### Step 5: Import Workflow

1. Download `food-delivery-whatsapp-workflow.json` from this repository
2. Open n8n in your browser
3. Click **"Workflows"** → **"Add Workflow"**
4. Click the three dots menu (⋮) → **"Import from File"**
5. Select `food-delivery-whatsapp-workflow.json`
6. Click **"Import"**
7. The workflow will open
8. Click the **"Active"** toggle to turn it ON (green)

**Your webhook URL is now ready!** It will look like:
- n8n Cloud: `https://[your-account].app.n8n.cloud/webhook/order-status-update`
- Self-hosted: `http://your-server:5678/webhook/order-status-update`

---

### Step 6: Test the System

Before integrating with your app, test that everything works.

#### Test 1: Order Received

```bash
curl -X POST YOUR_WEBHOOK_URL \
  -H 'Content-Type: application/json' \
  -d '{
    "orderStatus": "order_received",
    "orderId": "TEST-001",
    "customerName": "Test User",
    "customerPhone": "YOUR_PHONE_NUMBER",
    "orderItems": "2x Pizza, 1x Coke"
  }'
```

**Replace:**
- `YOUR_WEBHOOK_URL` with your actual webhook URL
- `YOUR_PHONE_NUMBER` with your test phone (format: `919876543210`, no + or 00)

**Expected result:** You should receive a WhatsApp message!

#### Test 2: In Process

```bash
curl -X POST YOUR_WEBHOOK_URL \
  -H 'Content-Type: application/json' \
  -d '{
    "orderStatus": "in_process",
    "orderId": "TEST-001",
    "customerName": "Test User",
    "customerPhone": "YOUR_PHONE_NUMBER",
    "estimatedTime": "25"
  }'
```

#### Test 3: Dispatched

```bash
curl -X POST YOUR_WEBHOOK_URL \
  -H 'Content-Type: application/json' \
  -d '{
    "orderStatus": "dispatched",
    "orderId": "TEST-001",
    "customerName": "Test User",
    "customerPhone": "YOUR_PHONE_NUMBER",
    "driverName": "Test Driver",
    "driverPhone": "1234567890",
    "trackingLink": "https://example.com/track/TEST-001"
  }'
```

**✅ If all 3 tests work, your system is ready!**

---

### Step 7: Integrate with Your App

Add webhook calls to your food delivery application.

#### PHP Example

```php
<?php
function sendOrderStatusUpdate($orderId, $status, $customerData) {
    $webhookUrl = 'YOUR_WEBHOOK_URL'; // Replace with your webhook URL

    $payload = [
        'orderStatus' => $status,
        'orderId' => $orderId,
        'customerName' => $customerData['name'],
        'customerPhone' => $customerData['phone']
    ];

    // Add status-specific data
    if ($status === 'order_received') {
        $payload['orderItems'] = $customerData['items'];
    } elseif ($status === 'in_process') {
        $payload['estimatedTime'] = $customerData['estimatedTime'];
    } elseif ($status === 'dispatched') {
        $payload['driverName'] = $customerData['driverName'];
        $payload['driverPhone'] = $customerData['driverPhone'];
        $payload['trackingLink'] = $customerData['trackingLink'];
    }

    $ch = curl_init($webhookUrl);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_HTTPHEADER, ['Content-Type: application/json']);
    curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($payload));

    $response = curl_exec($ch);
    curl_close($ch);
}

// Usage: When order is placed
sendOrderStatusUpdate('ORD-123', 'order_received', [
    'name' => $customer->name,
    'phone' => $customer->phone,
    'items' => '2x Pizza, 1x Coke'
]);
?>
```

#### Node.js Example

```javascript
const axios = require('axios');

async function sendOrderStatusUpdate(orderId, status, customerData) {
    const webhookUrl = 'YOUR_WEBHOOK_URL';

    const payload = {
        orderStatus: status,
        orderId: orderId,
        customerName: customerData.name,
        customerPhone: customerData.phone
    };

    if (status === 'order_received') {
        payload.orderItems = customerData.items;
    } else if (status === 'in_process') {
        payload.estimatedTime = customerData.estimatedTime;
    } else if (status === 'dispatched') {
        payload.driverName = customerData.driverName;
        payload.driverPhone = customerData.driverPhone;
        payload.trackingLink = customerData.trackingLink;
    }

    await axios.post(webhookUrl, payload);
}

// Usage: When order is placed
await sendOrderStatusUpdate('ORD-123', 'order_received', {
    name: customer.name,
    phone: customer.phone,
    items: '2x Pizza, 1x Coke'
});
```

#### Python Example

```python
import requests

def send_order_status_update(order_id, status, customer_data):
    webhook_url = 'YOUR_WEBHOOK_URL'

    payload = {
        'orderStatus': status,
        'orderId': order_id,
        'customerName': customer_data['name'],
        'customerPhone': customer_data['phone']
    }

    if status == 'order_received':
        payload['orderItems'] = customer_data['items']
    elif status == 'in_process':
        payload['estimatedTime'] = customer_data['estimatedTime']
    elif status == 'dispatched':
        payload['driverName'] = customer_data['driverName']
        payload['driverPhone'] = customer_data['driverPhone']
        payload['trackingLink'] = customer_data['trackingLink']

    requests.post(webhook_url, json=payload)

# Usage: When order is placed
send_order_status_update('ORD-123', 'order_received', {
    'name': customer.name,
    'phone': customer.phone,
    'items': '2x Pizza, 1x Coke'
})
```

---

## API Keys Configuration

### Required Keys

You need **2 API keys** from WhatsApp Business API:

| Key | Description | Where to Get | Example |
|-----|-------------|--------------|---------|
| **WHATSAPP_PHONE_NUMBER_ID** | Your WhatsApp Business phone number ID | Meta Business Manager → WhatsApp Manager → Phone Numbers | `102290129340398` |
| **WHATSAPP_ACCESS_TOKEN** | Permanent access token for API | Meta Business Manager → System Users → Generate Token | `EAAXXXxxxXXXxxx...` |

### Where to Add Keys

**Add keys in n8n Environment Variables ONLY**

✅ **Correct:** n8n Environment Variables
❌ **Wrong:** In workflow file, in application code, in Git repository

The workflow file (`food-delivery-whatsapp-workflow.json`) is already configured to read from environment variables. You don't need to edit it!

### Security Best Practices

1. ✅ Never share your Access Token publicly
2. ✅ Use environment variables (not hardcoded)
3. ✅ Enable HTTPS for webhook URL in production
4. ✅ Add webhook authentication in n8n settings
5. ✅ Regular backups - export workflow monthly
6. ✅ Rotate tokens every 3-6 months

---

## Webhook Integration

### Webhook Endpoint

```
POST https://your-n8n-url/webhook/order-status-update
Content-Type: application/json
```

### Payload Format

#### Order Received
```json
{
  "orderStatus": "order_received",
  "orderId": "ORD-12345",
  "customerName": "John Doe",
  "customerPhone": "1234567890",
  "orderItems": "2x Pizza, 1x Coke"
}
```

#### In Process
```json
{
  "orderStatus": "in_process",
  "orderId": "ORD-12345",
  "customerName": "John Doe",
  "customerPhone": "1234567890",
  "estimatedTime": "25"
}
```

#### Dispatched
```json
{
  "orderStatus": "dispatched",
  "orderId": "ORD-12345",
  "customerName": "John Doe",
  "customerPhone": "1234567890",
  "driverName": "Mike Smith",
  "driverPhone": "9876543210",
  "trackingLink": "https://track.yourapp.com/ORD-12345"
}
```

### Phone Number Format

**CRITICAL:** Phone numbers must be in international format **WITHOUT** `+` or `00`

| Country | Format | ✅ Correct | ❌ Wrong |
|---------|--------|-----------|----------|
| USA | 1AAABBBCCCC | `14155551234` | `+14155551234` |
| India | 91AAABBBCCCC | `919876543210` | `+919876543210` |
| UK | 44AAABBBBBBBB | `447911123456` | `0044791112345` |
| UAE | 971AABBBCCCC | `971501234567` | `+971501234567` |

---

## Testing

### Test Files Included

- `example-webhook-payloads.json` - Complete test payload examples
- `SETUP_CHECKLIST.md` - Step-by-step testing guide

### Quick Test Commands

See [Step 6: Test the System](#step-6-test-the-system) above for curl commands.

### Verify in n8n

1. Go to n8n dashboard
2. Click **"Executions"** in left sidebar
3. See all webhook calls and their results
4. Click on any execution to see detailed logs

### Verify in WhatsApp Manager

1. Go to Meta Business Manager → WhatsApp Manager
2. Click **"Insights"** → **"Messages"**
3. View delivery rates and analytics

---

## Troubleshooting

### No WhatsApp messages received?

**Check these in order:**

1. ✅ Are all templates **Approved** in Meta Business Manager?
   - Go to WhatsApp Manager → Message Templates
   - All 3 should show "Approved" status

2. ✅ Is the n8n workflow **Active**?
   - Green toggle should be ON

3. ✅ Are environment variables set correctly?
   - Check n8n → Settings → Environment Variables
   - Variable names must be exact: `WHATSAPP_PHONE_NUMBER_ID` and `WHATSAPP_ACCESS_TOKEN`

4. ✅ Is phone number format correct?
   - Should be: `919876543210` (no + or 00)
   - Should NOT be: `+919876543210`

5. ✅ Check n8n execution logs
   - n8n → Executions → Click on failed execution
   - Read error message

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `Template not found` | Template name spelling wrong | Check exact names: `order_received`, `order_in_process`, `order_dispatched` |
| `401 Unauthorized` | Wrong or expired Access Token | Regenerate token in Meta Business Manager |
| `404 Not Found` | Wrong Phone Number ID | Verify Phone Number ID in Meta Business Manager |
| `Parameter count mismatch` | Missing required fields in payload | Check all required fields are present |
| Workflow not responding | Workflow not activated | Activate workflow (green toggle) |

### Debug Mode

1. Open workflow in n8n
2. Click **"Execute Workflow"** button
3. Manually test with sample data
4. See exactly where it fails

---

## Production Deployment

### Pre-Production Checklist

- [ ] All 3 WhatsApp templates approved
- [ ] Environment variables configured in production n8n
- [ ] Webhook URL accessible from production server
- [ ] SSL/TLS configured (HTTPS)
- [ ] All 3 status types tested successfully
- [ ] Error handling implemented in your app
- [ ] Monitoring and logging set up
- [ ] Rate limiting configured
- [ ] Backup of workflow exported
- [ ] Team trained on troubleshooting

### Scaling Considerations

**WhatsApp API Tier Limits:**
- **Tier 1**: 1,000 unique customers per 24 hours
- **Tier 2**: 10,000 unique customers per 24 hours
- **Tier 3**: 100,000 unique customers per 24 hours

Meta automatically upgrades your tier as you maintain:
- Delivery rate > 95%
- Block rate < 1%
- Spam report rate < 0.1%

**For high volume:**
- Use n8n queue mode
- Implement retry logic with exponential backoff
- Monitor rate limits in WhatsApp Manager
- Consider load balancing for multiple n8n instances

### Monitoring

**Key Metrics:**
- Delivery Rate: Target > 95%
- Response Time: Target < 5 seconds
- Error Rate: Target < 1%
- Block Rate: Target < 1%

**Where to monitor:**
- n8n Dashboard → Executions
- Meta Business Manager → WhatsApp Manager → Insights

---

## FAQ

### How long does setup take?
**2-3 hours** total, including waiting for WhatsApp template approval (which can take 15 min - 24 hours).

### How much does it cost?
**WhatsApp:** First 1,000 conversations/month often free, then varies by country.
**n8n:** Free plan available, paid from $20/month.
**Total:** Can start with $0/month.

### Do I need coding skills?
Basic skills helpful. Integration code is provided in PHP, Node.js, and Python. Just copy-paste and replace webhook URL.

### Can I customize the messages?
Yes! Edit templates in Meta Business Manager and resubmit for approval.

### What if messages aren't delivered?
Check: Templates approved? Workflow active? Phone format correct? Environment variables set? See [Troubleshooting](#troubleshooting).

### Can I use this for other business types?
Yes! Just modify the templates. This pattern works for any business that needs status update notifications.

### Is this WhatsApp compliant?
Yes! Uses official WhatsApp Business API with approved TRANSACTIONAL templates.

### Can I add more status types?
Yes! Create additional templates in Meta Business Manager, add conditions in the n8n workflow Switch node, and add corresponding WhatsApp API nodes.

### How do I handle multiple languages?
Create language-specific templates in Meta Business Manager (e.g., `order_received_es` for Spanish). Add language detection in your app and route to appropriate template.

---

## File Structure

```
food-delivery-whatsapp-system/
├── README.md                              ← This file
├── food-delivery-whatsapp-workflow.json   ← Import to n8n
├── API_KEYS_CONFIGURATION.md              ← API keys setup guide
├── CUSTOMER_SETUP_GUIDE.md                ← Detailed setup instructions
├── CONFIGURATION_TEMPLATE.md              ← Configuration values template
├── SETUP_CHECKLIST.md                     ← Progress tracking checklist
├── example-webhook-payloads.json          ← Test payload examples
├── QUICK_VISUAL_GUIDE.md                  ← Visual diagrams
├── PROJECT_FLOWCHARTS.md                  ← Technical flowcharts
└── REFERENCE_CARD.md                      ← Developer quick reference
```

---

## Support

### Documentation

- **Setup Guide**: `CUSTOMER_SETUP_GUIDE.md` - Complete step-by-step
- **API Keys**: `API_KEYS_CONFIGURATION.md` - Where to add credentials
- **Testing**: `example-webhook-payloads.json` - Test payloads
- **Troubleshooting**: See [Troubleshooting](#troubleshooting) section above
- **Visual Guides**: `QUICK_VISUAL_GUIDE.md` - Diagrams and flowcharts

### External Resources

- **n8n Documentation**: https://docs.n8n.io
- **WhatsApp Cloud API**: https://developers.facebook.com/docs/whatsapp/cloud-api
- **Meta Business Help**: https://business.facebook.com/help
- **n8n Community**: https://community.n8n.io

---

## License

This project is provided as-is for use in your food delivery business. You may:
- ✅ Use it for your business
- ✅ Modify the templates
- ✅ Customize the workflow
- ✅ Add more features

You may NOT:
- ❌ Resell this system as-is
- ❌ Use for spam or unsolicited marketing (violates WhatsApp policy)

---

## Version

- **Version**: 1.0.0
- **Last Updated**: 2025-10-27
- **n8n Compatibility**: v1.0+
- **WhatsApp API Version**: v18.0+

---

## Quick Links

- [Quick Start](#quick-start) - Get started in 2-3 hours
- [Testing](#testing) - Test before going live
- [Troubleshooting](#troubleshooting) - Common issues and solutions
- [FAQ](#faq) - Frequently asked questions
- [API Keys Configuration](#api-keys-configuration) - Where to add credentials

---

**Ready to transform your customer communication with automated WhatsApp notifications?**

Start with the [Quick Start](#quick-start) section above! 🚀

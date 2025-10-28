# Food Delivery Telegram Notification System

Automated Telegram notification system for food delivery businesses. Send real-time order status updates to customers via Telegram Bot API using n8n workflow automation.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [What You'll Need](#what-youll-need)
- [Quick Start](#quick-start)
- [Detailed Setup Guide](#detailed-setup-guide)
  - [Step 1: Create Telegram Bot](#step-1-create-telegram-bot)
  - [Step 2: Install n8n](#step-2-install-n8n)
  - [Step 3: Configure Environment Variables](#step-3-configure-environment-variables)
  - [Step 4: Import Workflow](#step-4-import-workflow)
  - [Step 5: Test the System](#step-5-test-the-system)
  - [Step 6: Integrate with Your App](#step-6-integrate-with-your-app)
- [API Keys Configuration](#api-keys-configuration)
- [Webhook Integration](#webhook-integration)
- [Getting Customer Chat IDs](#getting-customer-chat-ids)
- [Testing](#testing)
- [Troubleshooting](#troubleshooting)
- [Production Deployment](#production-deployment)
- [FAQ](#faq)
- [Support](#support)
- [License](#license)

---

## Overview

This system automatically sends Telegram messages to your customers when their order status changes. It integrates seamlessly with any food delivery platform and uses Telegram's official Bot API.

### How It Works

```
Customer Orders → Your App → n8n Webhook → Telegram Bot API → Customer's Telegram
```

1. Customer places an order in your food delivery app
2. Your app calls the n8n webhook when order status changes
3. n8n workflow routes the message based on status
4. Telegram Bot API sends the message to customer
5. Customer receives instant notification on Telegram

### Message Types

**1. Order Received** - Confirms order placement
```
🎉 Order Received!

Hi John, we've received your order #12345
for 2x Pizza, 1x Coke. We're preparing it now!

Thank you for choosing us!
```

**2. In Process** - Kitchen preparation update
```
👨‍🍳 Order In Process

Hi John, your order #12345 is being prepared
by our kitchen!

⏱️ Estimated time: 25 minutes

We'll notify you when it's ready for dispatch!
```

**3. Dispatched** - Delivery driver details
```
🚀 Order Dispatched!

Hi John, your order #12345 is on its way!

👤 Driver: Mike Smith
📞 Contact: 9876543210

📍 Track your order: [tracking link]

Enjoy your meal!
```

---

## Features

- ✅ **Fully Automated** - No manual intervention required
- 📱 **Official Telegram Bot API** - Free and reliable
- 🔄 **Real-time Updates** - Instant notifications
- 🌐 **Platform Agnostic** - Works with PHP, Node.js, Python, Java, etc.
- 📊 **Easy Monitoring** - Track all messages from n8n dashboard
- 🔒 **Secure** - Uses environment variables for credentials
- 🎨 **Customizable** - Modify messages to match your brand
- 📈 **Scalable** - Handles from 10 to 100,000+ orders per day
- 💰 **100% Free** - No message limits or costs
- ⚡ **No Approval Required** - Start sending messages immediately

---

## What You'll Need

### Required Services

1. **Telegram Bot** (from @BotFather)
   - Completely free
   - No verification required
   - Created in 2 minutes

2. **n8n Platform** (Workflow Automation)
   - Free plan available
   - Cloud or self-hosted options
   - Get at: https://n8n.io

3. **Your Food Delivery System**
   - Any platform that can make HTTP requests
   - PHP, Node.js, Python, Java, etc.

### Estimated Costs

- **Telegram Bot API**: FREE (unlimited messages)
- **n8n Cloud**: Free plan available, paid plans from $20/month
- **n8n Self-hosted**: Free (just server costs)

**Total: $0-20/month**

---

## Quick Start

**Total setup time: 30-45 minutes**

1. ✅ Create Telegram bot (2-5 min)
2. ✅ Set up n8n and import workflow (10-15 min)
3. ✅ Configure bot token (2 min)
4. ✅ Test the system (5-10 min)
5. ✅ Integrate with your app (10-15 min)
6. ✅ Go live!

---

## Detailed Setup Guide

### Step 1: Create Telegram Bot

#### 1.1 Open Telegram and Find BotFather

1. Open Telegram app on your phone or desktop
2. Search for **@BotFather** (official bot with blue checkmark)
3. Click **"Start"** to begin conversation

#### 1.2 Create Your Bot

1. Send command: `/newbot`
2. BotFather asks: **"Alright, a new bot. How are we going to call it?"**
3. Enter your bot name (e.g., `My Food Delivery Bot`)
4. BotFather asks: **"Good. Now let's choose a username"**
5. Enter username ending in `bot` (e.g., `myfooddelivery_bot`)

#### 1.3 Get Your Bot Token

BotFather will reply with:
```
Done! Congratulations on your new bot.
You will find it at t.me/myfooddelivery_bot

Use this token to access the HTTP API:
1234567890:ABCdefGHIjklMNOpqrsTUVwxyz

Keep your token secure and store it safely...
```

**Copy and save this token!** You'll need it in Step 3.

---

### Step 2: Install n8n

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

### Step 3: Configure Environment Variables

Add your Telegram bot token to n8n.

#### For n8n Cloud:

1. Log in to your n8n account
2. Go to **Settings** → **Environment Variables**
3. Click **"Add Variable"**
4. Add variable:
   - **Name**: `TELEGRAM_BOT_TOKEN`
   - **Value**: [Your Bot Token from Step 1]
5. Click **"Save"**

#### For Self-Hosted n8n:

1. Create or edit: `~/.n8n/.env`
2. Add this line:
   ```bash
   TELEGRAM_BOT_TOKEN=1234567890:ABCdefGHIjklMNOpqrsTUVwxyz
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
  -e TELEGRAM_BOT_TOKEN="1234567890:ABCdefGHIjklMNOpqrsTUVwxyz" \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

---

### Step 4: Import Workflow

1. Download `food-delivery-telegram-workflow.json` from this repository
2. Open n8n in your browser
3. Click **"Workflows"** → **"Add Workflow"**
4. Click the three dots menu (⋮) → **"Import from File"**
5. Select `food-delivery-telegram-workflow.json`
6. Click **"Import"**
7. The workflow will open
8. Click the **"Active"** toggle to turn it ON (green)

**Your webhook URL is now ready!** It will look like:
- n8n Cloud: `https://[your-account].app.n8n.cloud/webhook/order-status-update`
- Self-hosted: `http://your-server:5678/webhook/order-status-update`

---

### Step 5: Test the System

Before integrating with your app, test that everything works.

**First, you need to get your Telegram Chat ID:**

1. Open Telegram
2. Search for your bot (e.g., `@myfooddelivery_bot`)
3. Click **"Start"** to begin conversation
4. Send any message to your bot (e.g., "Hello")
5. Visit this URL in browser (replace with your token):
   ```
   https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates
   ```
6. Look for `"chat":{"id":123456789}` in the response
7. Copy your chat ID (e.g., `123456789`)

#### Test 1: Order Received

```bash
curl -X POST YOUR_WEBHOOK_URL \
  -H 'Content-Type: application/json' \
  -d '{
    "orderStatus": "order_received",
    "orderId": "TEST-001",
    "customerName": "Test User",
    "customerChatId": "YOUR_CHAT_ID",
    "orderItems": "2x Pizza, 1x Coke"
  }'
```

**Replace:**
- `YOUR_WEBHOOK_URL` with your actual webhook URL
- `YOUR_CHAT_ID` with your Telegram chat ID

**Expected result:** You should receive a Telegram message!

#### Test 2: In Process

```bash
curl -X POST YOUR_WEBHOOK_URL \
  -H 'Content-Type: application/json' \
  -d '{
    "orderStatus": "in_process",
    "orderId": "TEST-001",
    "customerName": "Test User",
    "customerChatId": "YOUR_CHAT_ID",
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
    "customerChatId": "YOUR_CHAT_ID",
    "driverName": "Test Driver",
    "driverPhone": "1234567890",
    "trackingLink": "https://example.com/track/TEST-001"
  }'
```

**✅ If all 3 tests work, your system is ready!**

---

### Step 6: Integrate with Your App

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
        'customerChatId' => $customerData['telegram_chat_id']
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
    'telegram_chat_id' => $customer->telegram_chat_id,
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
        customerChatId: customerData.telegramChatId
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
    telegramChatId: customer.telegramChatId,
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
        'customerChatId': customer_data['telegram_chat_id']
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
    'telegram_chat_id': customer.telegram_chat_id,
    'items': '2x Pizza, 1x Coke'
})
```

---

## API Keys Configuration

### Required Keys

You need **1 API key** from Telegram Bot API:

| Key | Description | Where to Get | Example |
|-----|-------------|--------------|---------|
| **TELEGRAM_BOT_TOKEN** | Your bot token from BotFather | Telegram → @BotFather → /newbot | `1234567890:ABCdefGHIjklMNOpqrsTUVwxyz` |

### Where to Add Keys

**Add token in n8n Environment Variables ONLY**

✅ **Correct:** n8n Environment Variables
❌ **Wrong:** In workflow file, in application code, in Git repository

The workflow file (`food-delivery-telegram-workflow.json`) is already configured to read from environment variables. You don't need to edit it!

### Security Best Practices

1. ✅ Never share your Bot Token publicly
2. ✅ Use environment variables (not hardcoded)
3. ✅ Enable HTTPS for webhook URL in production
4. ✅ Add webhook authentication in n8n settings
5. ✅ Regular backups - export workflow monthly
6. ✅ Regenerate token if compromised (via @BotFather)

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
  "customerChatId": "123456789",
  "orderItems": "2x Pizza, 1x Coke"
}
```

#### In Process
```json
{
  "orderStatus": "in_process",
  "orderId": "ORD-12345",
  "customerName": "John Doe",
  "customerChatId": "123456789",
  "estimatedTime": "25"
}
```

#### Dispatched
```json
{
  "orderStatus": "dispatched",
  "orderId": "ORD-12345",
  "customerName": "John Doe",
  "customerChatId": "123456789",
  "driverName": "Mike Smith",
  "driverPhone": "9876543210",
  "trackingLink": "https://track.yourapp.com/ORD-12345"
}
```

---

## Getting Customer Chat IDs

To send messages to customers, you need their Telegram Chat IDs. There are several ways to collect this:

### Method 1: Registration Form (Recommended)

Add a Telegram username/chat ID field in your app registration:

```html
<label>Telegram Username (optional)</label>
<input type="text" name="telegram_username" placeholder="@username">
```

Then resolve username to chat ID using Telegram API or store chat ID when customer starts conversation with your bot.

### Method 2: Bot Start Link

1. Create a start link for your bot:
   ```
   https://t.me/myfooddelivery_bot?start=register
   ```
2. Ask customers to click this link during registration
3. Capture their chat ID when they click "Start"
4. Store chat ID in your database linked to their account

### Method 3: Manual Bot Interaction

1. Customer opens your bot on Telegram
2. Customer sends their order ID or email
3. Your bot captures their chat ID
4. You link it to their account in your database

**Example code to get updates:**
```bash
curl https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates
```

This returns all recent messages with chat IDs.

---

## Testing

### Test Files Included

- `food-delivery-telegram-workflow.json` - The n8n workflow
- Example curl commands provided above

### Quick Test Commands

See [Step 5: Test the System](#step-5-test-the-system) for curl commands.

### Verify in n8n

1. Go to n8n dashboard
2. Click **"Executions"** in left sidebar
3. See all webhook calls and their results
4. Click on any execution to see detailed logs

---

## Troubleshooting

### No Telegram messages received?

**Check these in order:**

1. ✅ Is the bot token correct?
   - Test it: `https://api.telegram.org/bot<TOKEN>/getMe`
   - Should return bot information

2. ✅ Is the n8n workflow **Active**?
   - Green toggle should be ON

3. ✅ Is the chat ID correct?
   - Get updates: `https://api.telegram.org/bot<TOKEN>/getUpdates`
   - Make sure customer has started conversation with bot

4. ✅ Check n8n execution logs
   - n8n → Executions → Click on failed execution
   - Read error message

### Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `Unauthorized` | Wrong bot token | Check token in environment variables |
| `Chat not found` | Wrong chat ID or user hasn't started bot | Ask customer to start conversation with bot |
| `Bad Request` | Malformed message | Check JSON payload format |
| Workflow not responding | Workflow not activated | Activate workflow (green toggle) |

### Debug Mode

1. Open workflow in n8n
2. Click **"Execute Workflow"** button
3. Manually test with sample data
4. See exactly where it fails

---

## Production Deployment

### Pre-Production Checklist

- [ ] Bot token configured in production n8n
- [ ] Webhook URL accessible from production server
- [ ] SSL/TLS configured (HTTPS)
- [ ] All 3 status types tested successfully
- [ ] Error handling implemented in your app
- [ ] Monitoring and logging set up
- [ ] Customer chat ID collection implemented
- [ ] Backup of workflow exported
- [ ] Team trained on troubleshooting

### Scaling Considerations

**Telegram Bot API:**
- No rate limits for most use cases
- Can handle millions of messages
- 100% free, no tier limits

**For high volume:**
- Use n8n queue mode
- Implement retry logic with exponential backoff
- Consider load balancing for multiple n8n instances
- Monitor Telegram API status: https://status.telegram.org/

### Monitoring

**Key Metrics:**
- Delivery Rate: Target > 99%
- Response Time: Target < 2 seconds
- Error Rate: Target < 0.1%

**Where to monitor:**
- n8n Dashboard → Executions

---

## FAQ

### How long does setup take?
**30-45 minutes** total. No waiting for approvals!

### How much does it cost?
**Telegram:** 100% FREE (unlimited messages)
**n8n:** Free plan available, paid from $20/month.
**Total:** $0-20/month.

### Do I need coding skills?
Basic skills helpful. Integration code is provided in PHP, Node.js, and Python. Just copy-paste and replace webhook URL.

### Can I customize the messages?
Yes! Edit the message text in the workflow nodes. No approval needed.

### What if messages aren't delivered?
Check: Bot token correct? Workflow active? Chat ID valid? Customer started bot? See [Troubleshooting](#troubleshooting).

### Can I use this for other business types?
Yes! Just modify the message templates. This pattern works for any business that needs status update notifications.

### How do I get customer chat IDs?
See [Getting Customer Chat IDs](#getting-customer-chat-ids) section above.

### Can I add more status types?
Yes! Add conditions in the Switch node and create new Telegram message nodes.

### How do I handle multiple languages?
Add language detection in your app and modify message text based on customer language. Or create separate workflows per language.

---

## Support

### Documentation

- **Telegram Bot API**: https://core.telegram.org/bots/api
- **n8n Documentation**: https://docs.n8n.io
- **BotFather Commands**: https://core.telegram.org/bots#botfather
- **n8n Community**: https://community.n8n.io

---

## License

This project is provided as-is for use in your food delivery business. You may:
- ✅ Use it for your business
- ✅ Modify the messages
- ✅ Customize the workflow
- ✅ Add more features

You may NOT:
- ❌ Resell this system as-is
- ❌ Use for spam or unsolicited messages (violates Telegram policy)

---

## Version

- **Version**: 1.0.0
- **Last Updated**: 2025-10-28
- **n8n Compatibility**: v1.0+
- **Telegram Bot API**: v6.0+

---

## Quick Links

- [Quick Start](#quick-start) - Get started in 30-45 minutes
- [Testing](#testing) - Test before going live
- [Troubleshooting](#troubleshooting) - Common issues and solutions
- [FAQ](#faq) - Frequently asked questions
- [Getting Customer Chat IDs](#getting-customer-chat-ids) - How to collect chat IDs

---

**Ready to transform your customer communication with automated Telegram notifications?**

Start with the [Quick Start](#quick-start) section above! 🚀

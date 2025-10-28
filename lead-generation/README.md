# Lead Generation Workflow

## Overview

This n8n workflow automates lead generation by searching for businesses based on location and industry using the Google Places API. It extracts valuable business information including names, phone numbers, websites, and addresses.

**🚀 New: Automatic Pagination** - The workflow now fetches up to **60 leads per search** (previously 20) by automatically handling Google Places API pagination with proper delays between requests.

### What This Workflow Does

**Input:**
- Location (city, state, or full address)
- Industry/Business type (e.g., restaurants, dentists, lawyers, gyms)
- Search radius (in meters)

**Output:**
- Business Name
- Phone Number
- Website
- Physical Address
- Business Status (operational/closed)

---

## Prerequisites

1. **n8n instance** (cloud or self-hosted)
2. **Google Cloud Platform (GCP) account** with billing enabled
3. **Google Places API enabled** with an API key

---

## Step 1: Set Up Google Cloud Platform (Free Tier)

### 1.1 Create a GCP Account

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Sign in with your Google account
3. If it's your first time, you'll get **$300 free credits** for 90 days
4. Set up billing (required even for free tier, but you won't be charged until you exceed limits)

### 1.2 Create a New Project

1. Click on the project dropdown at the top of the page
2. Click **"New Project"**
3. Name your project (e.g., "Lead Generation")
4. Click **"Create"**

### 1.3 Enable Required APIs

1. Go to **APIs & Services** > **Library**
2. Search for and enable the following APIs:
   - **Places API**
   - **Geocoding API**
3. Click **"Enable"** for each API

### 1.4 Create API Credentials

1. Go to **APIs & Services** > **Credentials**
2. Click **"Create Credentials"** > **"API Key"**
3. Your API key will be generated
4. **IMPORTANT:** Click on the API key to configure restrictions:
   - Under **"API restrictions"**, select **"Restrict key"**
   - Check only:
     - Places API
     - Geocoding API
   - Click **"Save"**
5. **Copy and save your API key securely**

### 1.5 Understanding Free Tier Limits

**Google Places API Free Tier:**
- $200 free credit per month
- After credits:
  - Place Search: $17 per 1000 requests
  - Place Details: $17 per 1000 requests
  - Geocoding: $5 per 1000 requests

**Monthly Free Usage Estimate:**
- Approximately **200-250 lead searches** per month within free tier
- Each search can return up to **60 businesses** (with automatic pagination)

**Tips to Stay Within Free Tier:**
1. Set up billing alerts in GCP
2. Use appropriate search radius
3. Be specific with industry keywords
4. Monitor usage in GCP Console

---

## Step 2: Set Up the Workflow in n8n

### 2.1 Import the Workflow

1. Open your n8n instance
2. Click **"Workflows"** > **"Add Workflow"**
3. Click the **three dots menu** (⋮) > **"Import from File"**
4. Select `lead-generation-workflow.json`
5. The workflow will be imported with all nodes configured

### 2.2 Configure Google Places API Credentials

1. In the imported workflow, click on any **HTTP Request** node (e.g., "Geocode Location")
2. Click on **"Credential to connect with"**
3. Click **"Create New"**
4. Select **"Query Auth"** credential type
5. Configure the credential:
   - **Name:** `Google Places API Key`
   - **Authentication:** Query Parameter
   - **Name:** `key`
   - **Value:** Paste your Google API key
6. Click **"Save"**
7. The credential will automatically apply to all HTTP Request nodes

### 2.3 Activate the Workflow

1. Click **"Save"** to save the workflow
2. Toggle the **"Active"** switch at the top right to activate

---

## Step 3: Using the Workflow

### 3.1 Configure Search Parameters

1. Click on the **"Input Parameters"** node
2. Modify the values:
   - **location:** Enter your target location (e.g., "Los Angeles, CA", "Miami Beach, FL", "10001")
   - **industry:** Enter business type (e.g., "coffee shops", "real estate agents", "plumbers")
   - **radius:** Search radius in meters (default: 5000 = 5km)

### 3.2 Run the Workflow

1. Click **"Test workflow"** button at the bottom
2. The workflow will execute through all nodes
3. View results in the **"Aggregate Leads"** node

### 3.3 Understanding the Output

The final output includes an array of businesses with:

```json
{
  "business_name": "Example Coffee Shop",
  "phone": "+1 555-123-4567",
  "website": "https://www.example.com",
  "address": "123 Main St, New York, NY 10001, USA",
  "business_status": "OPERATIONAL"
}
```

**Note:** Some businesses may not have all fields (website, phone, etc.). These will show as "N/A".

---

## Workflow Architecture

### Node Breakdown

1. **Manual Trigger:** Starts the workflow when you click "Test workflow"
2. **Input Parameters:** Set location, industry, and radius
3. **Geocode Location:** Converts location name to GPS coordinates
4. **Extract Coordinates:** Parses geocoding response
5. **Search Places with Pagination:** Finds businesses matching criteria (automatically fetches all pages up to 60 results)
6. **Extract Place IDs:** Gets unique IDs for each business
7. **Get Business Details:** Fetches detailed info (phone, website, etc.)
8. **Format Lead Data:** Cleans and structures the output
9. **Aggregate Leads:** Combines all leads into final output

### Data Flow

```
Location + Industry → Geocoding → Coordinates → Places Search →
Place IDs → Details API → Formatted Leads → Aggregated Output
```

---

## Exporting and Using Leads

### Method 1: Copy JSON Output

1. Click on the **"Aggregate Leads"** node after execution
2. Click on the **"JSON"** tab
3. Copy the output data
4. Paste into your CRM, spreadsheet, or database

### Method 2: Add Export Nodes (Optional Enhancement)

You can extend this workflow by adding output nodes:

#### A. Export to Google Sheets
1. Add a **"Google Sheets"** node after "Aggregate Leads"
2. Configure to append rows to a spreadsheet
3. Map fields: business_name, phone, website, address

#### B. Export to CSV
1. Add a **"Spreadsheet File"** node
2. Set format to CSV
3. Download or save to storage

#### C. Export to Airtable/Database
1. Add **"Airtable"** or **"PostgreSQL"** node
2. Configure database connection
3. Insert leads as new records

---

## Customization Options

### Adjust Search Radius

Modify the `radius` parameter (in meters):
- **1000** = 1km (very local)
- **5000** = 5km (neighborhood)
- **10000** = 10km (city-wide)
- **50000** = 50km (regional)

**Max:** 50,000 meters (50km)

### Industry Keywords

Be specific with your industry search:
- Good: "Italian restaurants", "personal injury lawyers", "yoga studios"
- Too broad: "business", "services"

### Filter by Business Status

Add a filter node after "Format Lead Data" to exclude closed businesses:

```javascript
if ($json.business_status === 'OPERATIONAL') {
  return $json;
}
```

### Add Email Extraction (Advanced)

Google Places API doesn't provide emails directly. To add email finding:

1. Add a **"HTTP Request"** node to scrape the website
2. Use regex to extract emails from website content
3. Common pattern: `/[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}/`

**Note:** Email scraping has legal considerations - ensure compliance with data protection laws.

---

## Troubleshooting

### Error: "Location not found"

**Solution:**
- Use more specific location (include city and state)
- Try coordinates instead: "40.7128,-74.0060"
- Verify spelling

### Error: "REQUEST_DENIED"

**Solution:**
- Check API key is correct
- Verify Places API and Geocoding API are enabled in GCP
- Check API key restrictions allow these APIs

### Error: "OVER_QUERY_LIMIT"

**Solution:**
- You've exceeded free tier limits
- Check usage in GCP Console
- Wait for monthly reset or enable billing

### No Results Returned

**Solution:**
- Increase search radius
- Use different industry keywords
- Try a more populated location
- Check if businesses exist in that area

### Missing Phone/Website Data

**Solution:**
- This is normal - not all businesses provide this info to Google
- Try different search criteria
- Consider alternative data sources for verification

---

## Best Practices

### 1. API Usage Optimization

- **Batch searches:** Group similar searches to minimize API calls
- **Cache results:** Save data locally to avoid duplicate searches
- **Use specific keywords:** Reduces irrelevant results

### 2. Data Quality

- **Verify leads:** Always validate phone/email before outreach
- **Check business status:** Filter out permanently closed businesses
- **Update regularly:** Business info changes; refresh leads periodically

### 3. Legal Compliance

- **GDPR/CCPA:** Ensure compliance when storing personal data
- **CAN-SPAM:** Follow regulations for email marketing
- **Do Not Call:** Check against DNC registries before calling

### 4. Cost Management

- **Set budget alerts** in GCP at $10, $50, $100
- **Monitor daily usage** in GCP Console
- **Use workflow sparingly** during testing
- **Disable workflow** when not in use

---

## Advanced Enhancements

### 1. Add Webhook Trigger

Replace manual trigger with webhook to run via API:
```
POST https://your-n8n.com/webhook/lead-generation
Body: { "location": "Austin, TX", "industry": "coffee shops" }
```

### 2. Schedule Regular Searches

Add a **"Schedule Trigger"** to run searches automatically:
- Daily: Update leads database
- Weekly: Discover new businesses
- Monthly: Refresh all data

### 3. Enrich with Additional Data

Add nodes to fetch:
- Social media profiles (LinkedIn, Facebook)
- Company reviews and ratings
- Business hours and photos
- Competitor analysis

### 4. Integration with CRM

Connect to popular CRMs:
- **Salesforce**
- **HubSpot**
- **Pipedrive**
- **Zoho CRM**

### 5. Lead Scoring

Add a **Code** node to score leads based on:
- Has website (+10 points)
- Has phone (+10 points)
- Operational status (+5 points)
- Location proximity (+variable)

---

## Example Use Cases

### 1. Local Business Outreach

**Scenario:** Marketing agency targeting local restaurants

**Configuration:**
- Location: "Brooklyn, NY"
- Industry: "restaurants"
- Radius: 3000 (3km)

**Result:** Up to 60 restaurant leads with contact info

### 2. B2B Sales Prospecting

**Scenario:** SaaS company targeting dental offices

**Configuration:**
- Location: "San Francisco, CA"
- Industry: "dental offices"
- Radius: 10000 (10km)

**Result:** Up to 60 dental practice leads

### 3. Real Estate Lead Generation

**Scenario:** Finding property management companies

**Configuration:**
- Location: "Miami, FL"
- Industry: "property management"
- Radius: 15000 (15km)

**Result:** Up to 60 property management leads

### 4. Multi-Location Campaigns

**Scenario:** National franchise recruitment

**Method:**
1. Create array of cities
2. Add **"Loop"** node to iterate through locations
3. Run workflow for each city
4. Combine all results

---

## API Reference

### Google Places API Endpoints Used

1. **Geocoding API**
   - Endpoint: `https://maps.googleapis.com/maps/api/geocode/json`
   - Purpose: Convert address to coordinates
   - Cost: $5 per 1000 requests

2. **Places Nearby Search**
   - Endpoint: `https://maps.googleapis.com/maps/api/place/nearbysearch/json`
   - Purpose: Find businesses in radius
   - Cost: $17 per 1000 requests

3. **Place Details**
   - Endpoint: `https://maps.googleapis.com/maps/api/place/details/json`
   - Purpose: Get detailed business info
   - Cost: $17 per 1000 requests

### Rate Limits

- **Queries per second (QPS):** 50 QPS per project
- **Daily quota:** Based on billing tier
- **Results per request:** Max 60 (3 pages of 20)

---

## Security Considerations

### Protecting Your API Key

1. **Never commit API keys to Git**
   - Use environment variables
   - Add to `.gitignore`

2. **Restrict API key usage**
   - Limit to specific APIs
   - Set HTTP referrer restrictions (for web apps)
   - Set IP restrictions (for server apps)

3. **Rotate keys regularly**
   - Change API keys every 90 days
   - Immediately rotate if compromised

4. **Monitor for unusual activity**
   - Check GCP logs regularly
   - Set up usage alerts
   - Review API call patterns

### Data Storage

- **Encrypt sensitive data** at rest
- **Use secure connections** (HTTPS)
- **Implement access controls** on lead databases
- **Regular backups** with encryption
- **Data retention policies** (auto-delete old leads)

---

## FAQ

**Q: How many leads can I generate per month for free?**
A: Approximately 200-250 searches, with each search returning up to 60 businesses (12,000-15,000 total leads with pagination enabled).

**Q: Can I get email addresses?**
A: Google Places API doesn't provide emails. You'd need to scrape websites or use third-party enrichment services.

**Q: How accurate is the data?**
A: Data accuracy depends on Google's database. Most phone numbers and addresses are 85-95% accurate.

**Q: Can I use this commercially?**
A: Yes, but ensure compliance with Google's Terms of Service and data protection laws.

**Q: What if I exceed the free tier?**
A: You'll be charged based on GCP pricing. Set up billing alerts to avoid surprises.

**Q: Can I export to Excel?**
A: Yes, use the JSON output and convert to CSV/Excel, or add a Spreadsheet node to the workflow.

**Q: How do I handle duplicate businesses?**
A: Add a deduplication node using the `place_id` field as a unique identifier.

**Q: Can I search multiple cities at once?**
A: Yes, wrap the workflow in a loop and provide an array of locations.

---

## Support and Resources

### Official Documentation

- [Google Places API Docs](https://developers.google.com/maps/documentation/places/web-service)
- [n8n Documentation](https://docs.n8n.io)
- [n8n Community Forum](https://community.n8n.io)

### Useful Links

- [GCP Console](https://console.cloud.google.com)
- [API Usage Dashboard](https://console.cloud.google.com/apis/dashboard)
- [GCP Billing](https://console.cloud.google.com/billing)

### Getting Help

1. Check this README first
2. Review n8n community forum for similar issues
3. Check Google Places API status page
4. Review GCP logs for error details

---

## License

This workflow is provided as-is for educational and commercial use. Ensure compliance with:
- Google Maps Platform Terms of Service
- Applicable data protection regulations (GDPR, CCPA, etc.)
- Local telemarketing and email marketing laws

---

## Version History

- **v1.1** (2025-10-28): Pagination Support
  - Added automatic pagination to fetch up to 60 results (instead of 20)
  - Handles Google Places API next_page_token automatically
  - Implements required 2-second delay between paginated requests
  - Up to 3x more leads per search

- **v1.0** (2025-10-27): Initial release
  - Basic lead generation workflow
  - Google Places API integration
  - Geocoding support
  - Comprehensive documentation

---

## Contributing

To improve this workflow:

1. Test with different industries and locations
2. Add new export formats (Airtable, HubSpot, etc.)
3. Implement email finding capabilities
4. Add lead scoring algorithms
5. Create templates for specific industries

---

## Conclusion

This lead generation workflow provides a powerful, cost-effective way to discover and collect business leads using Google's comprehensive business database. By staying within the free tier limits and following best practices, you can generate thousands of quality leads per month.

For questions or improvements, please refer to the n8n community or Google Cloud documentation.

Happy lead generating!

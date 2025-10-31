# Usage Guide: Lead Generation 1000+ Workflow

## Quick Start

This workflow can generate **1000+ unique business leads** for a single query (e.g., "Bangalore + dentists") by using a grid-based search strategy with pagination.

---

## How to Use

### Step 1: Import the Workflow

1. Open your n8n instance
2. Go to **Workflows** → **Add Workflow**
3. Click the **three dots menu** (⋮) → **"Import from File"**
4. Select `lead-generation-1000plus-workflow.json`
5. Click **Save**

### Step 2: Configure API Credentials

1. Click on any **HTTP Request** node (e.g., "Geocode Location")
2. Under **"Credential to connect with"**, select **"Query Auth"**
3. If you haven't created credentials yet:
   - Click **"Create New"**
   - **Name:** `Google Places API Key`
   - **Authentication:** Query Parameter
   - **Name:** `key`
   - **Value:** Your Google API key (from GCP Console)
   - Click **"Save"**

### Step 3: Set Input Parameters

Click on the **"Input Parameters"** node and configure:

#### Required Parameters:

| Parameter | Description | Example |
|-----------|-------------|---------|
| **location** | City or area to search | `"Bangalore, India"` |
| **industry** | Type of business | `"dentists"` |
| **gridSize** | Grid dimensions (3-7) | `5` (for 5×5 = 25 cells) |
| **coverageRadiusKm** | Radius to cover in km | `25` (covers 25km from center) |

#### Grid Size Guide:

| Grid Size | Total Cells | Theoretical Max Leads | Best For |
|-----------|-------------|----------------------|----------|
| **3×3** | 9 | ~540 | Small cities, specific neighborhoods |
| **4×4** | 16 | ~960 | Medium cities |
| **5×5** | 25 | ~1,500 | Large cities (recommended) |
| **6×6** | 36 | ~2,160 | Very large metropolitan areas |
| **7×7** | 49 | ~2,940 | Mega cities, extensive coverage |

**Note:** Actual unique leads are typically 60-80% of theoretical max due to deduplication.

### Step 4: Run the Workflow

1. Click **"Test workflow"** at the bottom
2. The workflow will execute:
   - Geocode your location
   - Calculate grid cells
   - Search each cell with pagination (up to 60 results per cell)
   - Deduplicate results
   - Fetch detailed business info
   - Format and aggregate all leads

3. **Execution time:** ~5-10 minutes depending on grid size

### Step 5: View Results

1. After execution completes, click on the **"Final Lead List"** node
2. View results in the **JSON** or **Table** tab
3. Each lead includes:
   - Business name
   - Phone number
   - Website
   - Full address
   - Business status (OPERATIONAL/CLOSED)
   - Rating
   - Number of reviews

---

## Example Configurations

### Example 1: Bangalore Dentists (1000+ leads)

```
location: "Bangalore, India"
industry: "dentists"
gridSize: 5
coverageRadiusKm: 25
```

**Expected results:** 800-1,200 unique dental practices
**Execution time:** ~6-8 minutes
**Estimated cost:** ~$20-25

### Example 2: New York Restaurants (1500+ leads)

```
location: "New York, NY"
industry: "restaurants"
gridSize: 6
coverageRadiusKm: 20
```

**Expected results:** 1,500-2,000 unique restaurants
**Execution time:** ~10-12 minutes
**Estimated cost:** ~$35-40

### Example 3: Austin Real Estate Agents (600+ leads)

```
location: "Austin, TX"
industry: "real estate agents"
gridSize: 4
coverageRadiusKm: 15
```

**Expected results:** 500-700 unique real estate agents
**Execution time:** ~5-6 minutes
**Estimated cost:** ~$12-15

### Example 4: London Coffee Shops (2000+ leads)

```
location: "London, UK"
industry: "coffee shops"
gridSize: 7
coverageRadiusKm: 30
```

**Expected results:** 1,800-2,500 unique coffee shops
**Execution time:** ~12-15 minutes
**Estimated cost:** ~$50-60

---

## Understanding the Workflow

### Node-by-Node Breakdown

1. **Manual Trigger**
   - Starts the workflow

2. **Input Parameters**
   - Sets location, industry, grid size, coverage radius

3. **Geocode Location**
   - Converts location name to GPS coordinates (lat/lng)
   - API: Google Geocoding API

4. **Calculate Grid**
   - Divides the area into a grid of cells
   - Each cell gets its own center coordinates
   - Calculates optimal search radius for each cell

5. **Store Grid Metadata**
   - Saves grid configuration for reference

6. **Split Grid Cells**
   - Creates separate workflow items for each grid cell
   - Enables parallel processing

7. **Search Cell with Pagination**
   - **This is where the magic happens!**
   - For EACH grid cell:
     - Searches Google Places API
     - Fetches up to 3 pages using `next_page_token`
     - Gets up to 60 results per cell
     - Includes 2.5-second delay between pages (Google requirement)

8. **Aggregate & Deduplicate**
   - Combines results from all cells
   - Removes duplicate businesses (using `place_id`)
   - Provides statistics: total found, unique count, duplicates removed

9. **Extract Place IDs**
   - Prepares unique business IDs for detail fetching

10. **Get Business Details**
    - Fetches detailed information for each unique business
    - API: Google Place Details API

11. **Format Lead Data**
    - Structures the data into clean, usable format

12. **Final Lead List**
    - Aggregates all formatted leads into final output

### Data Flow Diagram

```
Input (City + Industry + Grid Config)
    ↓
Geocode (Get Center Coordinates)
    ↓
Calculate Grid (Create 25 cells for 5×5)
    ↓
Split Cells (25 separate items)
    ↓
For Each Cell:
    ↓
    Search Places (Page 1) → 20 results
    ↓ (wait 2.5s)
    Search Places (Page 2) → 20 results
    ↓ (wait 2.5s)
    Search Places (Page 3) → 20 results
    ↓
    Cell Results: ~60 results
    ↓
Aggregate All Cells (25 × 60 = ~1,500 results)
    ↓
Deduplicate (Remove duplicates → ~1,000 unique)
    ↓
Get Details for Each Unique Business
    ↓
Format & Output Final Lead List
```

---

## Cost Analysis

### API Calls Breakdown (5×5 Grid Example)

| API Call | Quantity | Unit Cost | Total Cost |
|----------|----------|-----------|------------|
| Geocoding | 1 | $0.005 | $0.01 |
| Places Nearby Search | 75 (25 cells × 3 pages) | $0.017 | $1.28 |
| Place Details | ~1,000 (unique leads) | $0.017 | $17.00 |
| **TOTAL** | - | - | **~$18.30** |

### Free Tier Usage

Google Cloud offers **$200 free credit per month**.

**With free tier:**
- $200 ÷ $18.30 per search = **~10-11 full grid searches per month**
- Each search yields ~1,000 unique leads
- **Total: ~10,000-11,000 leads per month for FREE**

### Optimizing Costs

**1. Use smaller grids for less dense areas:**
- 3×3 grid = ~$6-8 per search
- 4×4 grid = ~$12-14 per search

**2. Adjust coverage radius:**
- Smaller radius = fewer cells needed = lower cost

**3. Skip low-result cells:**
- The workflow automatically continues even if some cells have no results
- This saves on pagination API calls for empty areas

**4. Cache results:**
- Save results to a database
- Reuse for 7-30 days before re-fetching

---

## Monitoring Execution

### During Execution

Watch the workflow progress in real-time:

1. **Node highlighting:** Active nodes are highlighted
2. **Console logs:** Check browser console for cell-by-cell progress
3. **Execution time:** Shows elapsed time at the bottom

### After Execution

Click on nodes to inspect:

- **Calculate Grid:** See grid configuration and total cells
- **Search Cell with Pagination:** See results per cell
- **Aggregate & Deduplicate:** See total vs unique counts
- **Final Lead List:** See complete lead list

### Key Metrics to Check

| Metric | Where to Find | What It Means |
|--------|---------------|---------------|
| Total cells searched | "Aggregate & Deduplicate" node | Number of grid cells processed |
| Results before dedup | "Aggregate & Deduplicate" node | Total results across all cells |
| Unique results | "Aggregate & Deduplicate" node | Final unique business count |
| Duplicates removed | "Aggregate & Deduplicate" node | Overlap between cells |

---

## Exporting Results

### Method 1: Copy JSON (Quick)

1. Click **"Final Lead List"** node
2. Click **"JSON"** tab
3. Copy the entire JSON array
4. Paste into your CRM, database, or spreadsheet tool

### Method 2: Export to CSV

1. Add a **"Spreadsheet File"** node after "Final Lead List"
2. Configure:
   - **Operation:** Create
   - **Format:** CSV
   - **File Name:** `leads_{{ $now.format('YYYY-MM-DD') }}.csv`
3. Download the generated CSV file

### Method 3: Send to Google Sheets

1. Add a **"Google Sheets"** node after "Final Lead List"
2. Configure:
   - **Operation:** Append
   - **Document:** Select your spreadsheet
   - **Sheet:** Select sheet name
3. Leads will automatically append to your sheet

### Method 4: Send to CRM

Add integration nodes for:
- **HubSpot** → Create contacts
- **Salesforce** → Create leads
- **Airtable** → Create records
- **PostgreSQL** → Insert rows

---

## Troubleshooting

### Issue: "Location not found"

**Solution:**
- Use more specific location format: "City, State, Country"
- Examples: "Bangalore, Karnataka, India" or "New York, NY, USA"
- Try coordinates: "12.9716, 77.5946"

### Issue: "API key not found"

**Solution:**
- Verify credentials are configured in HTTP Request nodes
- Check that credential name matches in all nodes
- Ensure API key is correctly copied (no extra spaces)

### Issue: Few results (< 500 unique leads)

**Causes & Solutions:**

1. **Small coverage area:**
   - Increase `coverageRadiusKm` (try 30-40km)

2. **Low business density:**
   - This is normal for rural areas or niche industries
   - Consider using broader industry keywords

3. **Grid too small:**
   - Increase `gridSize` from 3×3 to 5×5 or 6×6

4. **Industry keyword too specific:**
   - Instead of "cosmetic dentistry", use "dentists"
   - Instead of "Italian restaurants", use "restaurants"

### Issue: Execution timeout

**Solution:**
- n8n has execution timeout limits
- For very large grids (7×7+), consider:
  - Running workflow in cloud n8n (longer timeouts)
  - Splitting into multiple smaller workflows
  - Increasing n8n timeout settings

### Issue: "OVER_QUERY_LIMIT"

**Solution:**
- You've exceeded Google's free tier
- Check usage in [GCP Console](https://console.cloud.google.com/apis/dashboard)
- Either:
  - Wait for monthly reset (1st of next month)
  - Enable billing to continue beyond free tier

### Issue: High duplicate rate (> 50%)

**Explanation:**
- This is normal when:
  - Grid cells have high overlap
  - Business density is low
  - Coverage radius is large

**Not a problem:** Deduplication handles this automatically

---

## Best Practices

### 1. Start Small, Scale Up

First run:
- Use 3×3 grid
- Check results and costs
- Gradually increase to 4×4, 5×5, etc.

### 2. Match Grid Size to Area

| Area Type | Recommended Grid |
|-----------|------------------|
| Single neighborhood | 3×3 |
| Medium city | 4×4 |
| Large city | 5×5 |
| Metropolitan area | 6×6 |
| Mega city (Mumbai, Tokyo) | 7×7 |

### 3. Use Appropriate Keywords

**Good keywords:**
- "dentists", "restaurants", "gyms", "lawyers", "plumbers"

**Too broad:**
- "businesses", "services", "shops"

**Too narrow:**
- "vegan gluten-free restaurants" (won't trigger Google's index well)
- "motorcycle accident lawyers" (too specific)

### 4. Verify Data Quality

Not all businesses will have:
- Phone numbers (~80% have phones)
- Websites (~40-60% have websites)
- Complete addresses (~95% have addresses)

This is normal. Plan for data enrichment if needed.

### 5. Respect Rate Limits

The workflow includes built-in delays. Don't modify or remove them:
- 2.5 seconds between pagination requests
- This ensures Google doesn't reject requests

### 6. Set Up Billing Alerts

In GCP Console:
1. Go to **Billing** → **Budgets & alerts**
2. Create alert at: $50, $100, $150, $200
3. Receive email notifications before exceeding free tier

---

## Advanced: Customization

### Add Email Scraping

After "Get Business Details" node:

1. Add **HTTP Request** node
2. Fetch website content for businesses with websites
3. Add **Code** node to extract emails with regex:
   ```javascript
   const content = $input.item.json.body;
   const emailRegex = /[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}/g;
   const emails = content.match(emailRegex) || [];
   return { json: { ...input.item.json, email: emails[0] || 'N/A' } };
   ```

### Add Lead Scoring

After "Format Lead Data":

```javascript
let score = 0;
if ($json.website !== 'N/A') score += 10;
if ($json.phone !== 'N/A') score += 10;
if ($json.business_status === 'OPERATIONAL') score += 5;
if ($json.rating >= 4.0) score += 5;
if ($json.reviews_count >= 50) score += 5;

return { json: { ...$json, lead_score: score } };
```

### Filter by Business Status

After "Format Lead Data", add **Filter** node:

```javascript
return $json.business_status === 'OPERATIONAL';
```

### Schedule Automatic Runs

1. Replace **"Manual Trigger"** with **"Schedule Trigger"**
2. Set frequency (daily, weekly, monthly)
3. Automatically refresh leads in your database

---

## FAQ

**Q: How long does it take to generate 1000+ leads?**
A: Approximately 6-10 minutes for a 5×5 grid (25 cells).

**Q: Can I search multiple cities at once?**
A: Not in this workflow. Run separate executions for each city, or create a loop wrapper workflow.

**Q: Why do I get fewer than 1,500 leads with 5×5 grid?**
A: Deduplication removes 20-40% of results (businesses appearing in multiple cells). This is expected and ensures data quality.

**Q: Can I pause and resume execution?**
A: No, n8n executions run to completion. However, cell searches are independent, so partial results are usable.

**Q: Will this violate Google's Terms of Service?**
A: No, this workflow uses official APIs within documented limits and best practices.

**Q: How accurate is the data?**
A: Google Places data is typically 85-95% accurate. Always verify critical information before use.

**Q: Can I get emails directly?**
A: Google Places API doesn't provide emails. You'll need to scrape websites or use third-party enrichment services.

**Q: What if my city doesn't have 1000 businesses of that type?**
A: The workflow will return all available businesses. For niche industries or small cities, expect fewer results.

---

## Support

### Issues & Errors

1. Check this guide first
2. Review n8n execution logs for error details
3. Verify GCP API status: [status.cloud.google.com](https://status.cloud.google.com)
4. Check your API usage in [GCP Console](https://console.cloud.google.com/apis/dashboard)

### Getting Help

- **n8n Community:** [community.n8n.io](https://community.n8n.io)
- **Google Maps Platform Support:** [developers.google.com/maps/support](https://developers.google.com/maps/support)
- **n8n Documentation:** [docs.n8n.io](https://docs.n8n.io)

---

## Conclusion

This workflow enables **scalable lead generation** by combining:
- ✅ Grid-based geographic coverage
- ✅ Pagination to maximize results per area
- ✅ Automatic deduplication
- ✅ Comprehensive business details

With proper configuration, you can generate **1000+ high-quality leads** for any city and industry combination, all within Google's free tier limits.

Happy lead generating! 🚀

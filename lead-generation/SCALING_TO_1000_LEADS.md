# Scaling Lead Generation to 1000+ Results

## Problem: Google Places API Limitation

Google Places API has a **hard limit of 60 results per query** (3 pages × 20 results) using `next_page_token`.

For a query like "Bangalore + dentists", this means you can only get 60 dentists maximum with a single search, even though there may be thousands of dentists in Bangalore.

---

## Solution: Multi-Area Grid Search Strategy

To bypass this limitation and generate 1000+ leads, we use a **grid-based search** approach:

### How It Works

1. **Geocode the main location** (e.g., "Bangalore") → Get center coordinates
2. **Calculate city bounds** → Determine the area to cover
3. **Divide into grid cells** → Create multiple smaller search areas
4. **Search each cell** → Query Google Places API for each grid cell (with pagination)
5. **Aggregate results** → Combine all leads from all cells
6. **Deduplicate** → Remove duplicate businesses that appear in multiple cells

### Visual Example

```
Bangalore divided into 5×5 grid = 25 search areas
Each area can return up to 60 leads
Total potential: 25 × 60 = 1,500 leads

┌───┬───┬───┬───┬───┐
│ 1 │ 2 │ 3 │ 4 │ 5 │  Each cell is searched independently
├───┼───┼───┼───┼───┤
│ 6 │ 7 │ 8 │ 9 │10 │  Overlapping coverage ensures no leads missed
├───┼───┼───┼───┼───┤
│11 │12 │13 │14 │15 │  Deduplication removes businesses appearing
├───┼───┼───┼───┼───┤  in multiple adjacent cells
│16 │17 │18 │19 │20 │
├───┼───┼───┼───┼───┤
│21 │22 │23 │24 │25 │
└───┴───┴───┴───┴───┘
```

---

## Alternative Methods (Comparison)

### Method 1: Grid-Based Search ⭐ **RECOMMENDED**
**Pros:**
- Guarantees comprehensive coverage
- Predictable results (grid size = max leads)
- Works for any city size
- Efficient API usage

**Cons:**
- More API calls required
- Need to handle deduplication
- Slightly more complex implementation

**Cost Example:**
- 5×5 grid = 25 cells
- Each cell: 1 search + 3 pagination requests = 4 API calls
- Total API calls: ~100-150 (including detail requests)
- Cost: ~$2-3 per complete search

### Method 2: Multiple Keyword Variations
Search with different keywords:
- "dentists"
- "dental clinics"
- "dental offices"
- "orthodontists"
- "cosmetic dentistry"

**Pros:**
- Simple to implement
- May find businesses with different listings

**Cons:**
- Limited scalability
- High duplicate rate
- Unpredictable coverage
- Still capped around 200-300 unique leads

### Method 3: Text Search API (Instead of Nearby Search)
Use `textsearch` instead of `nearbysearch`:
```
/maps/api/place/textsearch/json?query=dentists+in+bangalore
```

**Pros:**
- Different result set than nearby search
- Can use more specific queries

**Cons:**
- Still limited to 60 results per query
- More expensive ($32 per 1000 vs $17)
- Less geographic precision

### Method 4: Combine Multiple Data Sources
Use Google Places + Yelp API + Bing Local API

**Pros:**
- More comprehensive data
- Different business databases

**Cons:**
- Multiple API subscriptions
- Complex data merging
- Higher costs

---

## Implementation Strategy

### Step 1: Calculate Grid Parameters

For a given city/radius, calculate:
```javascript
// Example: Bangalore coverage
const centerLat = 12.9716;  // Bangalore center
const centerLng = 77.5946;
const cityRadiusKm = 25;    // Cover 25km radius

// Grid configuration
const gridSize = 5;         // 5×5 = 25 cells
const cellSizeKm = (cityRadiusKm * 2) / gridSize;  // ~10km per cell
const searchRadiusM = (cellSizeKm / 2) * 1000;     // ~5000m search radius
```

### Step 2: Generate Grid Coordinates

```javascript
// Generate grid cell center points
const gridCells = [];
for (let row = 0; row < gridSize; row++) {
  for (let col = 0; col < gridSize; col++) {
    const lat = centerLat - cityRadiusKm/111.32 + (row + 0.5) * cellSizeKm/111.32;
    const lng = centerLng - cityRadiusKm/(111.32 * Math.cos(centerLat * Math.PI/180))
                + (col + 0.5) * cellSizeKm/(111.32 * Math.cos(lat * Math.PI/180));

    gridCells.push({
      id: row * gridSize + col + 1,
      lat: lat,
      lng: lng,
      radius: searchRadiusM
    });
  }
}
```

### Step 3: Loop Through Grid with Pagination

```javascript
// For each grid cell
for (const cell of gridCells) {
  // Search with pagination (up to 60 results per cell)
  let nextPageToken = null;
  let pageCount = 0;
  const maxPages = 3;

  do {
    const results = await searchPlaces(cell.lat, cell.lng, cell.radius, industry, nextPageToken);
    allLeads.push(...results.places);
    nextPageToken = results.next_page_token;
    pageCount++;

    if (nextPageToken) await delay(2000); // Google requirement
  } while (nextPageToken && pageCount < maxPages);
}
```

### Step 4: Deduplicate Results

```javascript
// Remove duplicates using place_id as unique identifier
const uniqueLeads = Array.from(
  new Map(allLeads.map(lead => [lead.place_id, lead])).values()
);
```

---

## n8n Workflow Implementation

### New Nodes Required

1. **Calculate Grid** (Code node)
   - Input: center location, coverage radius, grid size
   - Output: Array of grid cell coordinates

2. **Loop Over Grid** (Loop node)
   - Iterate through each grid cell

3. **Search Places with Pagination** (Code node)
   - Modified to accept grid cell coordinates
   - Fetches up to 60 results per cell using next_page_token

4. **Deduplicate Leads** (Code node)
   - Remove duplicates by place_id
   - Merge overlapping results

5. **Count & Report** (Code node)
   - Total leads found
   - Grid cells searched
   - Unique vs duplicate count

### Workflow Flow

```
Input Params → Geocode City → Calculate Grid → Loop Over Cells →
  → Search Places (with pagination) → Extract Place IDs →
  → Get Details → Format Leads → End Loop →
  → Deduplicate → Aggregate Final Results → Output
```

---

## Expected Results by Grid Size

| Grid Size | Total Cells | Max Leads* | API Calls | Est. Cost |
|-----------|-------------|-----------|-----------|-----------|
| 3×3 | 9 | 540 | ~50-70 | $1.00 |
| 4×4 | 16 | 960 | ~90-120 | $1.80 |
| 5×5 | 25 | 1,500 | ~140-180 | $2.80 |
| 6×6 | 36 | 2,160 | ~200-250 | $4.00 |
| 7×7 | 49 | 2,940 | ~270-350 | $5.50 |

\* Theoretical maximum before deduplication. Actual unique leads typically 60-80% of max due to overlapping areas.

---

## Optimizations

### 1. Adaptive Grid Sizing
Adjust grid size based on city population density:
- High density (NYC, Mumbai): 6×6 or 7×7 grid
- Medium density (Austin, Bangalore): 4×4 or 5×5 grid
- Low density (rural areas): 3×3 grid

### 2. Skip Empty Cells
Check first page results; if < 10 results, skip pagination for that cell.

### 3. Intelligent Overlap
Create 10-20% overlap between cells to ensure no missed businesses at boundaries.

### 4. Caching Results
Cache grid searches for 7-30 days to avoid re-fetching same data.

### 5. Progressive Loading
Return results as they're found instead of waiting for all grid cells.

---

## Cost Analysis

### Single Search (5×5 Grid for "Bangalore + dentists")

**API Calls Breakdown:**
- 1× Geocoding: $0.005
- 25× Places Nearby Search (with 3 pages each): 75 calls × $0.017 = $1.28
- ~1,200× Place Details (avg 48 unique per cell): 1,200 × $0.017 = $20.40

**Total Cost: ~$21.70 per full grid search**

**Free Tier Usage:**
- $200 free credit / $21.70 per search = ~9 full grid searches per month
- Each search yields ~1,000-1,500 unique leads
- **Total: ~9,000-13,500 leads per month on free tier**

### Optimization for Free Tier

To maximize free tier:
1. Use smaller grid (4×4) for less dense areas
2. Cache and reuse results
3. Only search high-value locations
4. Skip pagination if first page has few results

---

## Legal & Ethical Considerations

### Google Terms of Service
✅ **Allowed:**
- Caching results for up to 30 days
- Using data for your business operations
- Aggregating searches across geographic areas

❌ **Not Allowed:**
- Reselling raw Google data
- Creating a competing places database
- Scraping beyond API limits with multiple keys

### Data Protection
- **GDPR compliance**: Businesses are not personal data, but phone/email may require consent
- **Do Not Call registries**: Check before cold calling
- **CAN-SPAM**: Include unsubscribe for emails

---

## FAQ

**Q: Why not just increase the radius to 50km instead of using a grid?**
A: Google still limits to 60 results per query regardless of radius. A larger radius means you miss many businesses. Grid search ensures comprehensive coverage.

**Q: How many duplicates should I expect?**
A: Typically 20-40% duplicates with overlapping grid cells. This is expected and handled by deduplication.

**Q: Can I go beyond 5×5 grid?**
A: Yes, but consider costs. 7×7 (49 cells) = ~$35 per search. Make sure your lead value justifies the cost.

**Q: Will this violate Google's rate limits?**
A: No. Default is 50 QPS per project. Grid search with 2-second delays between pages stays well under limits.

**Q: How long does a full grid search take?**
A: 5×5 grid with pagination: ~5-8 minutes (due to 2-second delays between page requests).

---

## Next Steps

1. ✅ Implement pagination with `next_page_token`
2. ✅ Create grid calculation logic
3. ✅ Add loop structure for grid cells
4. ✅ Implement deduplication
5. ⏳ Test with "Bangalore + dentists"
6. ⏳ Optimize for cost and performance

---

## References

- [Google Places API - Nearby Search](https://developers.google.com/maps/documentation/places/web-service/search-nearby)
- [Google Places API - Pagination](https://developers.google.com/maps/documentation/places/web-service/search-nearby#PlaceSearchPaging)
- [Google Places API - Pricing](https://developers.google.com/maps/billing/gmp-billing)
- [n8n Loop Node Documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.splitinbatches/)

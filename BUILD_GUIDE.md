# 🛠️ Power BI Build Guide (Step by Step)

Follow these steps to build the dashboard yourself in **Power BI Desktop** (free, Windows).
Estimated time: **3–4 hours** spread over a few sessions.

> 💻 On a Mac? Power BI Desktop is Windows-only. Use **Tableau Public** (free) with the same data and layout,
> or run Power BI through a Windows virtual machine.

---

## Step 1: Load the data (15 min)
1. Download and install **Power BI Desktop** from the Microsoft Store.
2. **Home → Get data → Text/CSV** and load all 4 files from the `data/` folder:
   `customers.csv`, `products.csv`, `orders.csv`, `order_items.csv`.
3. Click **Transform data** (Power Query) and check the column types:
   - `orders[order_date]` and `customers[signup_date]` → **Date**
   - `unit_price`, `unit_cost`, `discount` → **Decimal number**
   - All `_id` columns and `quantity` → **Whole number**
4. Click **Close & Apply**.

## Step 2: Build the data model (15 min)
Go to **Model view** and make sure these relationships exist (drag one column onto the other):

| From (many) | To (one) |
|---|---|
| `order_items[order_id]` | `orders[order_id]` |
| `order_items[product_id]` | `products[product_id]` |
| `orders[customer_id]` | `customers[customer_id]` |
| `orders[order_date]` | `Date[Date]` *(after Step 3)* |

All relationships should be **many-to-one (\*:1)** with a **single** cross-filter direction.
This is a **star schema**, so mention that term in interviews.

## Step 3: Create the Date table (10 min)
1. **Modeling → New table** and paste the `Date` table from [`dax/measures.dax`](dax/measures.dax).
2. **Table tools → Mark as date table → Date**.
3. Select the `Month` column → **Sort by column → Month Num** (otherwise months sort alphabetically).
4. Create the relationship `orders[order_date]` → `Date[Date]`.

## Step 4: Add the DAX measures (30 min)
1. **Home → Enter data** → name the table `_Measures` → Load. This keeps all measures in one place.
2. Select `_Measures` and click **New measure** for each measure in [`dax/measures.dax`](dax/measures.dax).
3. Format them in **Measure tools**:
   - Revenue / profit / AOV → **Currency**, 0–2 decimals
   - `%` measures → **Percentage**, 1 decimal

✅ **Check your numbers:** in a Card visual, `Net Revenue` should show **$354,734.95** and `Total Orders` should show **2,008**.
These match the SQL project, which proves your model is correct.

## Step 5: Page 1, "Executive Overview" (60 min)
Match the layout in [`images/dashboard_mockup.png`](images/dashboard_mockup.png):

| Visual | Fields |
|---|---|
| **6 Cards** (top row) | Net Revenue, Gross Profit, Profit Margin %, Total Orders, Avg Order Value, Repeat Customer % |
| **Line chart** (area) | X-axis: `Date[Year-Month]` · Y-axis: Net Revenue |
| **Clustered bar chart** | Y-axis: `products[category]` · X-axis: Net Revenue · Tooltip: Profit Margin % |
| **Bar chart** | `customers[region]` · Net Revenue |
| **Donut chart** | `customers[segment]` · Net Revenue |
| **Donut chart** | `orders[channel]` · Net Revenue |
| **4 Slicers** (top) | `Date[Year]`, `customers[segment]`, `customers[region]`, `orders[channel]` (set style to **Dropdown**) |

## Step 6: Page 2, "Product Performance" (30 min)
| Visual | Fields |
|---|---|
| **Scatter chart** | X: Net Revenue · Y: Profit Margin % · Values: `products[product_name]` · Legend: `category` |
| **Table** (Top 10) | product_name, category, Units Sold, Net Revenue, Profit Margin % → *Filters pane: Top N = 10 by Net Revenue* |
| **Matrix** | Rows: category → product_name · Values: Net Revenue, Gross Profit, Profit Margin % |
| **Column chart** | `order_items[Discount Band]` (calculated column) · Profit Margin % |

## Step 7: Page 3, "Customers & Operations" (30 min)
| Visual | Fields |
|---|---|
| **Cards** | Active Customers, Repeat Customer %, Revenue per Customer, Lost Revenue |
| **Table** (Top 10 customers) | customer_name, segment, region, Total Orders, Net Revenue |
| **Clustered column** | `customers[segment]` · Revenue per Customer |
| **Bar chart** | `products[category]` · Return Rate %, Cancel Rate % |

## Step 8: Polish (30 min)
- **View → Themes**: pick one clean theme and use the same colors on every page.
- Add a **title bar** (rectangle + text box) on each page.
- Add **page navigation buttons** (Insert → Buttons → Navigator → Page navigator).
- Turn on **data labels** on bar charts; remove unnecessary gridlines.
- Use **Edit interactions** so slicers filter every visual.

## Step 9: Publish to GitHub (15 min)
1. Save the file as `ecommerce_sales_dashboard.pbix` in this folder.
2. Take a screenshot of each page and save them in `images/` as:
   `page1_overview.png`, `page2_products.png`, `page3_customers.png`.
3. In `README.md`, replace the mockup image with your real screenshots (see the comment in the README).
4. Upload the `.pbix` and screenshots to this GitHub repo.
5. *(Optional)* **File → Publish to web** (requires a free Power BI work account) and add the live link to the README.

---

### 💬 Interview talking points
- "I built a **star schema** with a dedicated **date table** to enable time-intelligence measures like YoY and YTD."
- "I validated the Power BI measures against my **SQL results** ($354,735 revenue, 2,008 orders)."
- "The dashboard showed that **Electronics has the highest revenue but the lowest margin (21%)**, so I recommended promoting higher-margin categories."

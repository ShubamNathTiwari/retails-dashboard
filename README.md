# 🛒 Retail Sales Dashboard — Power BI

A single-page Power BI report that gives retail teams an at-a-glance view of sales performance across regions, categories, brands, stores, and customers, with month-over-month trend analysis.

---

## 📸 Dashboard Preview

![Sales Dashboard](screenshot_of_my_dashboard.png)

---

## 📁 Project Structure

```
retail-powerbi/
├── Retail_dashboard.pbix           # Main Power BI report file
├── physical_data_model.docx        # Data model diagram (SmartArt)
├── screenshot_of_my_dashboard.png  # Dashboard preview
├── screenshot_of_model_view.png    # Power BI model view
└── README.md
```

---

## 📊 Key Metrics (KPI Cards)

| Metric | Value |
|---|---|
| Total Sales | 49.64 M |
| Total Profit | 11.23 M |
| Average Sales | 9.93 K |
| Total Customers | 500 |
| Total Orders | 5 K |

---

## 📈 Visuals

| Visual | Type | Description |
|---|---|---|
| **Total Sales by Region** | Clustered Bar Chart | Compares sales across South, West, North, and East regions |
| **Total Sales by Category** | Treemap | Sales distribution across Home, Clothing, Grocery, Electronics, and Beauty |
| **Brand Performance across Stores** | Grouped Bar Chart | Side-by-side brand comparison (BrandA–E) per store |
| **Top 10 Customers** | Horizontal Bar Chart | Highest-spending customers ranked by revenue |
| **Previous Month vs Current Month Sales** | Line Chart | Month-to-date sales trend overlaid against the prior month |

---

## 🗂️ Data Model

The report uses a **star schema** with one fact table and four dimension tables.

![Model View](screenshot_of_model_view.png)

```
Dim_Calendar ──┐
Dim_Product  ──┤
               ├──► Fact_Sales
Dim_Customer ──┤
Dim_Store    ──┘
```

### Tables

#### `Fact_Sales`
Central transaction table — one row per order line.

| Column | Type | Description |
|---|---|---|
| `OrderID` | Text | Unique order identifier |
| `OrderDate` | Date | Foreign key → `Dim_Calendar` |
| `ProductID` | Text | Foreign key → `Dim_Product` |
| `CustomerID` | Text | Foreign key → `Dim_Customer` |
| `StoreID` | Text | Foreign key → `Dim_Store` |
| `CostPrice` | Decimal (Σ) | Cost of goods |
| `DiscountPct` | Decimal (Σ) | Discount percentage applied |
| `Profit` | Decimal | Profit per line |
| `Quantity` | Integer (Σ) | Units sold |
| `PaymentMode` | Text | Payment method used |

#### `Dim_Calendar`
| Column | Description |
|---|---|
| `Date` | Full date (marked as Date Table) |
| `Month` | Month number |
| `Quarter` | Quarter number |
| `Year` | Calendar year |
| `Total Sales (SUMX)` | Calculated measure |

#### `Dim_Product`
| Column | Description |
|---|---|
| `ProductID` | Primary key |
| `ProductName` | Product display name |
| `Category` | Product category (Beauty, Clothing, Electronics, Grocery, Home) |
| `Brand` | Brand name (BrandA – BrandE) |

#### `Dim_Customer`
| Column | Description |
|---|---|
| `CustomerID` | Primary key |
| `CustomerName` | Customer display name |
| `City` | Customer city |
| `Region` | Sales region (East, West, North, South) |

#### `Dim_Store`
| Column | Description |
|---|---|
| `Store Key` | Primary key |
| `StoreID` | Store identifier (S1 – S5) |
| `SalespersonID` | Assigned salesperson |
| `Channel` | Sales channel |

### Relationships

| From | To | Cardinality |
|---|---|---|
| `Fact_Sales[ProductID]` | `Dim_Product[ProductID]` | Many-to-One (`*` → `1`) |
| `Fact_Sales[CustomerID]` | `Dim_Customer[CustomerID]` | Many-to-One (`*` → `1`) |
| `Fact_Sales[OrderDate]` | `Dim_Calendar[Date]` | Many-to-One (`*` → `1`) |
| `Fact_Sales[StoreID]` | `Dim_Store[StoreID]` | Many-to-One (`*` → `1`) |

---

## 🎛️ Filters & Slicers

| Filter | Options |
|---|---|
| **Year** | 2022, 2023, 2024, 2025 |
| **Month** | Range slider: 1 – 12 |
| **Region** | East, West, North, South |
| **Brand** | BrandA, BrandB, BrandC, BrandD, BrandE |
| **Category** | Beauty, Clothing, Electronics, Grocery, Home |
| **StoreID** | S1, S2, S3, S4, S5 |

All slicers cross-filter every visual on the page.

---

## ⚙️ DAX Measures (Key)

```dax
Total Sales = SUM(Fact_Sales[SalesAmount])

Total Profit = SUM(Fact_Sales[Profit])

Average Sales = AVERAGE(Fact_Sales[SalesAmount])

Total Customers = DISTINCTCOUNT(Fact_Sales[CustomerKey])

Total Orders = COUNTROWS(Fact_Sales)

-- Month-to-date sales (current month)
DatesMtD = CALCULATE([Total Sales], DATESMTD(Dim_Calendar[Date]))

-- Previous month sales (for trend line comparison)
PreviousMonth =
    CALCULATE(
        [Total Sales],
        DATEADD(Dim_Calendar[Date], -1, MONTH)
    )
```

---

## 🚀 Getting Started

### Prerequisites
- [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (latest version recommended)

### Steps

1. **Clone or download** this repository.
2. Open `Retail_dashboard.pbix` in Power BI Desktop.
3. If prompted, update the **data source path** under:
   `Home → Transform Data → Data Source Settings`
4. Click **Refresh** to load the latest data.
5. Use the slicers on the right panel to explore the data.

---

## 🔄 Refreshing Data

| Scenario | Steps |
|---|---|
| Local file source | Update file path in Data Source Settings, then Refresh |
| Database / cloud source | Update credentials and connection string |
| Scheduled refresh | Publish to Power BI Service and configure Gateway + scheduled refresh |

---

## 📌 Notes

- The **Previous Month vs Current Month Sales** line chart uses `DATESMTD` and `DATEADD` time-intelligence functions; ensure the `Dim_Calendar` table is marked as a **Date Table** in Power BI for these to work correctly.
- The **Brand Performance across Stores** chart requires `StoreID` and `Brand` fields to be in the same visual filter context.
- All five stores are labelled S1–S5 and all five brands BrandA–BrandE in the current dataset.

---

## 🛠️ Customisation

- **Add a new region:** Insert rows in `Dim_Customer` / `Dim_Store` with the new region value; the slicer auto-populates.
- **Add a new category:** Insert rows in `Dim_Product`; the treemap and slicer update on next refresh.
- **Change colour theme:** `View → Themes` in Power BI Desktop.
- **Add a drill-through page:** Right-click any visual → Drill through → Add drill-through page.

---

## 📄 License

This project is for internal / educational use. Replace this section with your organisation's licence terms before sharing externally.

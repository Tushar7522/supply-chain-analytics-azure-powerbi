# supply-chain-analytics-azure-powerbi 

# 🚚 Supply Chain Analytics using Azure Data Factory, Azure Synapse Analytics & Power BI

> An end-to-end cloud-based supply chain analytics solution — from raw CSV ingestion to executive-ready Power BI dashboards.

---

## 📌 Project Overview

This project demonstrates a complete **modern data engineering and analytics pipeline** built on Microsoft Azure. Raw e-commerce supply chain data was ingested into **Azure Data Lake Storage Gen2 (supplyc hainstorage96)**, transformed using **Azure Data Factory (adf-supplychain20)** with two pipelines — a **Mapping Data Flow** for Bronze→Silver and a **Power Query** activity for Silver→Gold — then loaded into an **Azure Synapse Analytics Dedicated SQL Pool (supplydedicated)**, and finally visualized through an interactive, multi-page **Power BI Dashboard**.

The solution enables business stakeholders to monitor revenue trends, customer behavior, product performance, logistics efficiency, and geographic sales distribution — all in one place.

---

## 🏗️ Solution Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                      END-TO-END PIPELINE FLOW                       │
└─────────────────────────────────────────────────────────────────────┘

   📄 Raw CSV Dataset (SupplyChainDataset)
         │
         ▼
   ☁️  Azure Data Lake Storage Gen2
   Storage Account: supplyc hainstorage96 (East US)
   ┌──────────────────────────────────────────┐
   │  🟫 bronze  →  SupplyChainDataset (CSV)  │
   │  🥈 silver  →  transformed-data          │
   │  🥇 gold    →  Fact_orders               │
   │              →  Dim_customer             │
   │              →  Dim_product              │
   │              →  Dim_category             │
   │              →  Dim_Department           │
   └──────────────────────────────────────────┘
         │
         ▼
   🔄 Azure Data Factory: adf-supplychain20 (East US)
   ┌──────────────────────────────────────────┐
   │  Pipeline 1: pl_bronze_to_silver         │
   │    └─ dataflow1 (Mapping Data Flow)      │
   │       source1 → derivedColumn → sink1    │
   │                                          │
   │  Pipeline 2: pl_silver_to_gold           │
   │    └─ Power Query Activity               │
   │       Queries: UserQuery, Dim_customer,  │
   │       Dim_product, Dim_category,         │
   │       Dim_department                     │
   └──────────────────────────────────────────┘
         │
         ▼
   🏛️  Azure Synapse Analytics
   Workspace: supply-synapse-workspace (Central US)
   ┌──────────────────────────────────────────┐
   │  Dedicated SQL Pool: supplydedicated     │
   │  Database: supplydedicated               │
   │  Tables: dbo.FactOrders                  │
   │          dbo.DimCustomer                 │
   │          dbo.DimProduct                  │
   │          dbo.DimCategory                 │
   │          dbo.DimDepartment               │
   └──────────────────────────────────────────┘
         │
         ▼
   📊 Power BI Dashboard
   ┌──────────────────────────────────────────┐
   │  5 Report Pages                          │
   │  DAX Measures & KPIs                     │
   │  Interactive Slicers & Maps              │
   └──────────────────────────────────────────┘
```

---

## 🛠️ Technology Stack

| Layer | Tool / Service | Resource Name |
|---|---|---|
| **Cloud Platform** | Microsoft Azure | rg-supplychain-project |
| **Data Storage** | Azure Data Lake Storage Gen2 | supplyc hainstorage96 (East US) |
| **Data Transformation** | Azure Data Factory V2 | adf-supplychain20 (East US) |
| **Transformation (Bronze→Silver)** | ADF Mapping Data Flow | dataflow1 |
| **Transformation (Silver→Gold)** | ADF Power Query Activity | powerquery1 |
| **Data Warehouse** | Azure Synapse Analytics | supply-synapse-workspace (Central US) |
| **SQL Pool** | Dedicated SQL Pool | supplydedicated |
| **Visualization** | Power BI Desktop | SupplyChain_Dashboard.pbix |
| **Query Language** | T-SQL | Synapse SQL Scripts |
| **Calculation Engine** | DAX | Power BI Measures |

---

## 📁 Repository Structure

```
Supply-Chain-Analytics-Azure-Synapse-PowerBI/
│
├── README.md
│
├── 01_Architecture/
│   ├── Resource_Group.png
│   └── Solution_Architecture.png
│
├── 02_Data_Lake/
│   ├── Storage_Account_Containers.png
│   ├── Bronze_Container.png
│   ├── Silver_Container.png
│   └── Gold_Container.png
│
├── 03_Azure_Data_Factory/
│   ├── pl_bronze_to_silver_dataflow.png
│   ├── pl_silver_to_gold_pipeline.png
│   └── powerquery1_editor.png
│
├── 04_Synapse_Data_Warehouse/
│   ├── FactOrders.sql
│   ├── DimCustomer.sql
│   ├── DimProduct.sql
│   ├── DimCategory.sql
│   ├── DimDepartment.sql
│   ├── CopyIntoScripts.sql
│   └── Synapse_Tables.png
│
├── 05_Data_Model/
│   ├── Star_Schema.png
│   └── Relationship_Model.png
│
├── 06_PowerBI/
│   ├── SupplyChain_Dashboard.pbix
│   ├── Executive_Summary.png
│   ├── Customer_Analytics.png
│   ├── Product_Analytics.png
│   ├── Logistics_Analytics.png
│   └── Geographic_Analytics.png
│
├── 07_DAX_Measures/
│   └── DAX_Measures.md
│
└── 08_Documentation/
    ├── Project_Report.pdf
    └── Data_Dictionary.xlsx
```

---

## ☁️ Azure Resource Group

All resources are deployed under a single resource group: **rg-supplychain-project**

| Resource Name | Type | Location |
|---|---|---|
| `adf-supplychain20` | Data Factory (V2) | East US |
| `supply-synapse-workspace` | Synapse Workspace | Central US |
| `supplyc hainstorage56` | Storage Account | Central US |
| `supplyc hainstorage96` | Storage Account (ADLS Gen2) | East US |
| `supplydedicated` | Dedicated SQL Pool | Central US |

---

## ⚙️ Data Engineering Process

### 1️⃣ Azure Data Lake — Medallion Architecture

Storage account **supplyc hainstorage96** hosts three containers following a Bronze → Silver → Gold medallion pattern:

| Container | Contents | Description |
|---|---|---|
| 🟫 **bronze** | `SupplyChainDataset/` | Raw CSV file uploaded as-is — no transformation |
| 🥈 **silver** | `transformed-data/` | Cleaned and type-corrected data from Mapping Data Flow |
| 🥇 **gold** | `Fact_orders/`, `Dim_customer/`, `Dim_product/`, `Dim_category/`, `Dim_Department/` | Business-modeled, query-ready tables output by Power Query |

---

### 2️⃣ Azure Data Factory — Two-Pipeline Architecture

Two pipelines handle separate transformation stages:

#### Pipeline 1: `pl_bronze_to_silver` — Mapping Data Flow

Handles raw data cleaning using **dataflow1**, a visual Mapping Data Flow with three steps:

```
source1  ──────►  derivedColumn  ──────►  sink1
(Import from         (Creating/updating    (Export to
 supplychainproperties1)  columns: Type,    DelimitedText1)
                     Days for shipping,
                     Benefit per order,
                     Sales per customer...)
```

- **source1** — reads raw CSV from the Bronze container (`supplychainproperties1` dataset)
- **derivedColumn** — calculates and standardizes columns: `Type`, `Days_for_shipping_real`, `Days_for_shipment_scheduled`, `Benefit_per_order`, `Sales_per_customer`
- **sink1** — writes cleaned output to the Silver container as delimited text

#### Pipeline 2: `pl_silver_to_gold` — Power Query Activity

Handles business modeling using **powerquery1**, an ADF Power Query activity with 6 queries:

| Query Name | Purpose |
|---|---|
| `UserQuery` (FactOrders) | Main fact table — all 34 columns, 99+ rows per batch |
| `Dim_customer` | Distinct customer dimension |
| `Dim_product` | Product catalog dimension |
| `Dim_category` | Category lookup dimension |
| `Dim_department` | Department lookup dimension |

**Applied Steps in Power Query (UserQuery):**
1. **Source** — connects to Silver layer transformed-data
2. **Changed column types** — enforces correct data types
3. **Removed columns** — drops unnecessary raw fields
4. **Reordered columns** — aligns to warehouse schema column order

The Power Query editor shows **34 columns, 99+ rows** loaded and validated in 4.31 seconds.

---

### 3️⃣ Azure Synapse Analytics — Data Warehouse

**Workspace:** `supply-synapse-workspace` | **SQL Pool:** `supplydedicated` | **Database:** `supplydedicated`

Five tables are created in the `dbo` schema. `FactOrders` uses **HASH distribution** on `Order_Id` with a **Clustered Columnstore Index** for optimal analytical query performance. All dimension tables use **ROUND_ROBIN + HEAP** for fast bulk loading.

**Table Summary:**

| Table | Type | Distribution | Columns |
|---|---|---|---|
| `FactOrders` | Fact | HASH(Order_Id) + CCI | 34 |
| `DimCustomer` | Dimension | ROUND_ROBIN + HEAP | 10 |
| `DimProduct` | Dimension | ROUND_ROBIN + HEAP | 4 |
| `DimCategory` | Dimension | ROUND_ROBIN + HEAP | 2 |
| `DimDepartment` | Dimension | ROUND_ROBIN + HEAP | 2 |

---

**FactOrders — Create & Load:**

```sql
CREATE TABLE FactOrders
(
    Order_Id                    INT,
    Days_for_shipping_real      INT,
    Days_for_shipment_scheduled INT,
    Benefit_per_order           FLOAT,
    Sales_per_customer          FLOAT,
    Delivery_Status             VARCHAR(100),
    Late_delivery_risk          INT,
    Category_Id                 INT,
    Customer_Id                 INT,
    Latitude                    FLOAT,
    Longitude                   FLOAT,
    Market                      VARCHAR(100),
    Order_City                  VARCHAR(100),
    Order_Country               VARCHAR(100),
    Order_Customer_Id           INT,
    Order_Date                  VARCHAR(100),
    Order_Item_Cardprod_Id      INT,
    Type                        VARCHAR(50),
    Order_Item_Discount         FLOAT,
    Order_Item_Discount_Rate    FLOAT,
    Order_Item_Id               INT,
    Order_Item_Product_Price    FLOAT,
    Order_Item_Profit_Ratio     FLOAT,
    Order_Item_Quantity         INT,
    Sales                       FLOAT,
    Order_Item_Total            FLOAT,
    Order_Profit_Per_Order      FLOAT,
    Order_Region                VARCHAR(100),
    Order_State                 VARCHAR(100),
    Order_Status                VARCHAR(100),
    Order_Zipcode               VARCHAR(50),
    Product_Card_Id             INT,
    Shipping_Date               VARCHAR(100),
    Shipping_Mode               VARCHAR(100)
)
WITH
(
    DISTRIBUTION = HASH(Order_Id),
    CLUSTERED COLUMNSTORE INDEX
);

COPY INTO FactOrders
FROM 'https://supplychainstorage96.dfs.core.windows.net/gold/supplychain-analytics/Fact_orders/*.csv'
WITH
(
    FILE_TYPE      = 'CSV',
    CREDENTIAL     = (IDENTITY = 'Managed Identity'),
    FIRSTROW       = 2,
    FIELDTERMINATOR = ',',
    FIELDQUOTE     = '"',
    ROWTERMINATOR  = '0x0A'
);
```

---

**DimCustomer — Create & Load:**

```sql
CREATE TABLE DimCustomer
(
    Customer_Id       INT,
    Customer_Fname    VARCHAR(100),
    Customer_Lname    VARCHAR(100),
    Customer_Email    VARCHAR(255),
    Customer_Country  VARCHAR(100),
    Customer_City     VARCHAR(100),
    Customer_Segment  VARCHAR(100),
    Customer_State    VARCHAR(100),
    Customer_Street   VARCHAR(255),
    Customer_Zipcode  VARCHAR(50)
)
WITH (DISTRIBUTION = ROUND_ROBIN, HEAP);

COPY INTO DimCustomer
FROM 'https://supplychainstorage96.dfs.core.windows.net/gold/supplychain-analytics/Dim_customer/*.csv'
WITH
(
    FILE_TYPE      = 'CSV',
    CREDENTIAL     = (IDENTITY = 'Managed Identity'),
    FIRSTROW       = 2,
    FIELDTERMINATOR = ',',
    FIELDQUOTE     = '"',
    ROWTERMINATOR  = '0x0A'
);
```

---

**DimProduct — Create & Load:**

```sql
CREATE TABLE DimProduct
(
    Product_Card_Id      INT,
    Product_Category_Id  INT,
    Product_Description  VARCHAR(500),
    Product_Image        VARCHAR(1000)
)
WITH (DISTRIBUTION = ROUND_ROBIN, HEAP);

COPY INTO DimProduct
FROM 'https://supplychainstorage96.dfs.core.windows.net/gold/supplychain-analytics/Dim_product/*.csv'
WITH
(
    FILE_TYPE      = 'CSV',
    CREDENTIAL     = (IDENTITY = 'Managed Identity'),
    FIRSTROW       = 2,
    FIELDTERMINATOR = ',',
    FIELDQUOTE     = '"',
    ROWTERMINATOR  = '0x0A'
);
```

---

**DimCategory — Create & Load:**

```sql
CREATE TABLE DimCategory
(
    Category_Id    INT,
    Category_Name  VARCHAR(200)
)
WITH (DISTRIBUTION = ROUND_ROBIN, HEAP);

COPY INTO dbo.DimCategory
FROM 'https://supplychainstorage96.dfs.core.windows.net/gold/supplychain-analytics/Dim_category/*.csv'
WITH
(
    FILE_TYPE      = 'CSV',
    CREDENTIAL     = (IDENTITY = 'Managed Identity'),
    FIRSTROW       = 2,
    FIELDTERMINATOR = ',',
    FIELDQUOTE     = '"',
    ROWTERMINATOR  = '0x0A'
);
```

---

**DimDepartment — Create & Load:**

```sql
CREATE TABLE DimDepartment
(
    Department_Id    INT,
    Department_Name  VARCHAR(200)
)
WITH (DISTRIBUTION = ROUND_ROBIN, HEAP);

COPY INTO dbo.DimDepartment
FROM 'https://supplychainstorage96.dfs.core.windows.net/gold/supplychain-analytics/Dim_Department/*.csv'
WITH
(
    FILE_TYPE      = 'CSV',
    CREDENTIAL     = (IDENTITY = 'Managed Identity'),
    FIRSTROW       = 2,
    FIELDTERMINATOR = ',',
    FIELDQUOTE     = '"',
    ROWTERMINATOR  = '0x0A'
);
```

---

### 4️⃣ Data Model — Star Schema

The warehouse is modeled as a **Star Schema** in Power BI with `fact_orders` at the center and four dimension tables. All relationships are **one-to-many (1:*)**.

```
         ┌──────────────────────────┐         ┌─────────────────────┐
         │       DimCustomer        │         │    DimDepartment    │
         │  Customer City           │         │  Department Id      │
         │  Customer Country        │         │  Department Name    │
         │  Customer Email          │         └──────────┬──────────┘
         │  Customer Fname ...      │                    │ 1
         └────────────┬─────────────┘                   │
                      │ 1                               │
                      │                                 │
         ┌────────────▼─────────────────────────────────▼──────────┐
         │                      fact_orders                         │
         │  Benefit per order      │  Category Id                   │
         │  Customer Id            │  Days for shipment (scheduled) │
         │  Days for shipping (real)│  Delivery Status              │
         │  Department Id          │  Late_delivery_risk            │
         │  Latitude  ...          │                                │
         └──────────────┬──────────────────────┬────────────────────┘
                        │ *                    │ *
                        │ 1                    │ 1
         ┌──────────────▼──────────┐  ┌────────▼────────────────┐
         │       DimProduct        │  │      DimCategory        │
         │  Product Card Id        │  │  Category Id            │
         │  Product Category Id    │  │  Category Name          │
         │  Product Image          │  └─────────────────────────┘
         │  Product Name           │
         │  Product Price          │
         └─────────────────────────┘
```

**Relationships (all One-to-Many):**

| One (1) Side | Many (*) Side | Join Key |
|---|---|---|
| `DimCustomer` | `fact_orders` | `Customer Id` |
| `DimDepartment` | `fact_orders` | `Department Id` |
| `DimProduct` | `fact_orders` | `Product Card Id` |
| `DimCategory` | `fact_orders` | `Category Id` |

**Table columns visible in Power BI model:**

| Table | Columns |
|---|---|
| `fact_orders` | Benefit per order, Category Id, Customer Id, Days for shipment (scheduled), Days for shipping (real), Delivery Status, Department Id, Late_delivery_risk, Latitude, + more |
| `DimCustomer` | Customer City, Customer Country, Customer Email, Customer Fname, + more |
| `DimProduct` | Product Card Id, Product Category Id, Product Image, Product Name, Product Price |
| `DimCategory` | Category Id, Category Name |
| `DimDepartment` | Department Id, Department Name |

---

## 📊 Power BI Dashboard

The dashboard has **5 report pages**, each targeting a different business audience.

---

### Page 1 — Business Performance Overview

**Audience:** C-Suite / Executives

**KPIs:** Total Revenue **$36.78M** | Total Orders **66K** | Total Profit **$3.97M** | Late Delivery Rate **54.82%** | Profit Margin **10.78%** | Total Customers **20.7K**

**Visuals:**
- Revenue Trend by Year (2015–2018) — peaked at $12.3M in 2015 & 2016
- Monthly Sales vs Previous Year with YOY Growth %
- Orders by Market — LATAM (52K) leads, followed by Europe (50K)
- Revenue by Market — Europe ($10.9M) leads, LATAM ($10.3M) second
- Monthly Profit Trend — peaks in Jan ($367K) and Aug–Sep ($360K)

![Business Performance Overview](06_PowerBI/Executive_Summary.png)

---

### Page 2 — Customer Analytics

**Audience:** Sales & CRM Teams

**KPIs:** Total Customers **20.7K** | Average Order Value **$559.45** | Average Orders Per Customer **3.18** | Revenue Per Customer **$1.78K**

**Visuals:**
- Sales by Customer Segment — Consumer 51.91% ($19.1M) | Corporate 30.36% ($11.17M) | Home Office 17.73% ($6.52M)
- Top Customers by Revenue — Mary leads at $13.0M
- Payment Method — Debit 38.54% | Transfer 27.49% | Payment 22.94% | Cash 11.02%
- Customer Geographic Distribution (Bing Map)
- Customer Count by Segment — Consumer 51.79% (10.7K) | Corporate 30.21% (6.24K) | Home Office 18% (3.72K)

![Customer Analytics](06_PowerBI/Customer_Analytics.png)

---

### Page 3 — Product Analytics

**Audience:** Product & Merchandising Teams

**KPIs:** Total Products Sold **180.5K** | Average Product Price **$166.41** | Total Product Catalog **118 SKUs**

**Visuals:**
- Top Products by Revenue — Field & Stream Sportsman ($6.9M #1), Perfect Fitness ($4.4M #2)
- Revenue by Category — Fishing ($6.9M) | Cleats ($4.4M) | Camping & Hiking ($4.1M)
- Top Products by Profit — Field & Stream ($756.2K) | Perfect Fitness ($493.8K)
- Profit by Department — Fan Shop ($1,834.2K) | Apparel ($881.9K) | Golf ($497.5K)

![Product Analytics](06_PowerBI/Product_Analytics.png)

---

### Page 4 — Supply Chain & Logistics

**Audience:** Operations & Logistics Teams

**KPIs:** Late Delivery Rate **54.82%** ⚠️ | Average Shipping Days **3.50** | Shipping Variance **0.57**

**Visuals:**
- Shipping Variance Trend — 0.564 (2015) → 0.569 (2016) → 0.562 (2017) → 0.613 (2018) 📈
- Delivery Status — Late 54.82% (36.05K) | On Time 23.01% (15.13K) | Advanced 17.83% (11.72K) | Cancelled 4.34% (2.86K)
- Late Delivery Risk by Shipping Mode — Standard Class 41.45% | Second Class 27.27% | First Class 26.79% | Same Day 4.5%
- Avg Delivery Time by Mode — Standard 4.00 days | Second Class 3.99 days | First Class 2.00 days | Same Day 0.48 days

![Supply Chain Analytics](06_PowerBI/Logistics_Analytics.png)

---

### Page 5 — Geographic Analysis

**Audience:** Regional Sales & Strategy Teams

**KPIs:** Total Countries **164** | Total Regions **23**

**Visuals:**
- Top States by Orders — Inglaterra (2.6K) | Île-de-France (1.7K) | California (1.7K) | North Rhine-Westphalia (1.2K)
- Global Sales Map — bubble map showing concentration in Europe, North America, and Southeast Asia
- Sales by Region — Western Europe ($5.9M) | Central America ($5.7M) | South America ($3.0M)
- Toggle: Total Sales ↔ Total Profit views

![Geographic Analysis](06_PowerBI/Geographic_Analytics.png)

---

## 🧮 Advanced DAX Measures

```dax
-- Profit Margin %
Profit Margin % =
DIVIDE(SUM(FactOrders[Order_Profit_Per_Order]), SUM(FactOrders[Sales]), 0) * 100

-- Late Delivery Rate %
Late Delivery Rate % =
DIVIDE(
    COUNTROWS(FILTER(FactOrders, FactOrders[Delivery_Status] = "Late delivery")),
    COUNTROWS(FactOrders),
    0
) * 100

-- Shipping Variance (Actual - Scheduled Days)
Shipping Variance =
AVERAGE(FactOrders[Days_for_shipping_real]) - AVERAGE(FactOrders[Days_for_shipment_scheduled])

-- Average Order Value
Average Order Value =
DIVIDE(SUM(FactOrders[Sales]), DISTINCTCOUNT(FactOrders[Order_Id]), 0)

-- Revenue per Customer
Revenue per Customer =
DIVIDE(SUM(FactOrders[Sales]), DISTINCTCOUNT(FactOrders[Customer_Id]), 0)

-- YOY Growth %
YOY Growth % =
VAR CurrentSales = SUM(FactOrders[Sales])
VAR PriorSales   = CALCULATE(SUM(FactOrders[Sales]), SAMEPERIODLASTYEAR('Date'[Date]))
RETURN DIVIDE(CurrentSales - PriorSales, PriorSales, 0) * 100

-- Product Revenue Rank
Product Revenue Rank =
RANKX(
    ALL(DimProduct[Product_Card_Id]),
    CALCULATE(SUM(FactOrders[Sales])),
    ,
    DESC,
    DENSE
)

-- Average Discount Rate
Avg Discount Rate =
AVERAGE(FactOrders[Order_Item_Discount_Rate]) * 100

-- Total Profit
Total Profit = SUM(FactOrders[Order_Profit_Per_Order])

-- Total Revenue
Total Revenue = SUM(FactOrders[Sales])

-- Total Orders
Total Orders = DISTINCTCOUNT(FactOrders[Order_Id])
```

---

## 📈 Key Business Insights

| Insight | Value |
|---|---|
| 🔴 Late Delivery Rate | **54.82%** — majority of orders arrive late |
| ⏱️ Shipping Variance | **+0.57 days** above schedule, worsening each year |
| 📈 Top Revenue Market | **Europe — $10.9M** |
| 🌎 Top Orders Market | **LATAM — 52K orders** |
| 🛍️ Top Revenue Category | **Fishing — $6.9M** |
| 🏬 Most Profitable Department | **Fan Shop — $1.83M** |
| 👤 Top Customer Segment | **Consumer — 51.91% of sales** |
| 💳 Most Used Payment Method | **Debit — 38.54%** |
| 🌍 Top Region by Sales | **Western Europe — $5.9M** |
| 📦 Highest Risk Shipping Mode | **Standard Class — 41.45% of late deliveries** |
| 📅 Revenue Peak Year | **2015 & 2016 — $12.3M each** |
| 🏆 Top Customer by Revenue | **Mary — $13.0M** |

---





---

> ⭐ If this project helped you, please give it a star!

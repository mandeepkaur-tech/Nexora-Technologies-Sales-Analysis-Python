<div align="center">

# 📊 Nexora Technologies — Sales Analysis & Business Intelligence

**An end-to-end Python & pandas project** that cleans four messy e-commerce datasets, integrates them into a single analytical dataset, and turns 805 orders from 397 customers into a full sales, profitability, and customer-behaviour report.

[![Python](https://img.shields.io/badge/Python-3.13-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-Data%20Wrangling-150458?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![NumPy](https://img.shields.io/badge/NumPy-Numerical%20Computing-013243?logo=numpy&logoColor=white)](https://numpy.org/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-11557C)](https://matplotlib.org/)
[![Seaborn](https://img.shields.io/badge/Seaborn-Statistical%20Viz-4C72B0)](https://seaborn.pydata.org/)

</div>

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Key Results](#-key-results)
- [Repository Structure](#-repository-structure)
- [Datasets](#-datasets)
- [Data Cleaning Summary](#-data-cleaning-summary)
- [Analysis & Visualizations](#-analysis--visualizations)
  - [1. Monthly Sales Trend](#1-monthly-sales-trend)
  - [2. Sales by Category](#2-sales-by-category)
  - [3. Revenue Contribution by Category](#3-revenue-contribution-by-category)
  - [4. Top 10 Products by Sales](#4-top-10-products-by-sales)
  - [5. Bottom 10 Products by Sales](#5-bottom-10-products-by-sales)
  - [6. Top 10 Customers by Sales](#6-top-10-customers-by-sales)
  - [7. Customer Segmentation by Spend](#7-customer-segmentation-by-spend)
  - [8. Repeat vs One-Time Customers](#8-repeat-vs-one-time-customers)
  - [9. Sales by City](#9-sales-by-city)
  - [10. Customer Distribution by City](#10-customer-distribution-by-city)
  - [11. Monthly Customer Signup Trend](#11-monthly-customer-signup-trend)
  - [12. Profitability Analysis (Sales vs Profit)](#12-profitability-analysis-sales-vs-profit)
  - [13. Correlation Analysis](#13-correlation-analysis)
  - [14. Sales Amount Outlier Analysis](#14-sales-amount-outlier-analysis)
- [Category Profitability](#-category-profitability)
- [Business Recommendations](#-business-recommendations)
- [Tech Stack](#-tech-stack)
- [How to Run](#-how-to-run)
- [Documentation](#-documentation)
- [Author & Contact](#-author--contact)

---

## 🧭 Overview

Nexora Technologies is an e-commerce electronics retailer selling across eight Indian cities. This project analyzes its transactional data to answer one core question:

> **Is strong revenue translating into strong profit — and where exactly is the business winning or losing money?**

The workflow covers the full analytics lifecycle:

```
Raw CSVs → Data Quality Checks → Cleaning & Standardization → Transformation
→ Dataset Integration → KPI Analysis → Product / Customer / Geographic Analysis
→ Advanced EDA → Business Recommendations
```

All analysis was performed in `Sales_Analysis_Python_Project.ipynb` using **Python, Pandas, NumPy, Matplotlib,** and **Seaborn**.

---

## 🎯 Key Results

| Metric | Value |
|---|---|
| **Total Sales** | ₹152,903,055.50 |
| **Total Profit** | −₹1,725,258.50 (net loss) |
| **Overall Profit Margin** | −1.13% |
| **Total Orders** | 805 |
| **Total Customers** | 397 |
| **Repeat Customer Rate** | 60.7% (241 of 397) |
| **Peak Sales Month** | January (₹21.11M) |
| **Most Profitable Category** | Tablet (+7.00% margin) |
| **Largest Loss-Making Category** | Smartphone (−9.20% margin) |
| **Top City by Sales** | Delhi (₹43.11M, 28% of total) |

> ⚠️ **Headline insight:** Despite ₹152.9M in revenue, Nexora runs an overall loss. Revenue leadership and profitability leadership are *not* held by the same category — see [Category Profitability](#-category-profitability).

---

## 📂 Repository Structure

```
nexora-sales-analysis/
├── data/
│   ├── messy_customers.csv        # Raw customer data (520 rows × 4 cols)
│   ├── messy_orders.csv           # Raw order data (1,240 rows × 4 cols)
│   ├── messy_order_items.csv      # Raw order-item data (2,050 rows × 5 cols)
│   └── messy_products.csv         # Raw product data (90 rows × 4 cols)
├── notebook/
│   └── Sales_Analysis_Python_Project.ipynb   # Full analysis notebook
├── docs/
│   └── Project-Documentation.docx            # Formal report (Word)
├── assets/
│   └── *.png                                 # Chart exports used in this README
└── README.md
```

> 💡 Place this README, the `assets/` folder of chart images, your CSVs, and the notebook in the structure above when you push to GitHub so all image links resolve correctly.

---

## 🗃 Datasets

| Dataset | Rows (raw) | Columns | Description |
|---|---|---|---|
| `messy_customers.csv` | 520 | 4 | Customer ID, name, city, signup date |
| `messy_orders.csv` | 1,240 | 4 | Order ID, customer ID, order date, payment method |
| `messy_order_items.csv` | 2,050 | 5 | Order-item ID, order ID, product ID, quantity, unit price |
| `messy_products.csv` | 90 | 4 | Product ID, name, category, base price |
| **Integrated `sales` dataset** | **1,359** | **20** | Final merged analytical table |

**Relationship model:**

```
Customers ──(customer_id)──▶ Orders ──(order_id)──▶ Order Items ──(product_id)──▶ Products
```

---

## 🧹 Data Cleaning Summary

Every table was profiled, de-duplicated, standardized, and validated before integration.

| Table | Duplicates Removed | Key Issues Fixed |
|---|---|---|
| Customers | 20 (520 → 500) | Inconsistent city casing/spelling standardized (e.g. `DELHI`/`delhi`/`Bengalore` → `Delhi`/`Bangalore`); missing names/cities filled as `Unknown` |
| Orders | 40 (1,240 → 1,200) | Mixed date formats parsed with `pd.to_datetime(format="mixed")`; payment method labels normalized (`COD`/`Cash on Delivery` → `Cash`) |
| Products | 10 (90 → 80) | Category naming merged (`Smart Phone` → `Smartphone`); missing `base_price` imputed with **category-level median** |
| Order Items | 50 (2,050 → 2,000) | Invalid (≤0) quantities removed; missing quantity imputed with median; missing `unit_price` backfilled from product `base_price` |

**Result:** 0 duplicate rows and 0 missing values across all four tables after cleaning.

```python
# Example — standardizing inconsistent city names
city_map = {"bengaluru": "Bangalore", "bengalore": "Bangalore",
            "delhi": "Delhi", "mumbai": "Mumbai"}
customer["city"] = (
    customer["city"].fillna("Unknown").str.strip()
    .str.lower().replace(city_map).str.title()
)
```

**Derived fields after merging:**

| Field | Formula |
|---|---|
| `sales_amount` | `quantity × unit_price` |
| `profit` | `(unit_price − base_price) × quantity` |
| `profit_margin` | `profit ÷ sales_amount × 100` |

---

## 📈 Analysis & Visualizations

### 1. Monthly Sales Trend
![Monthly Sales Trend](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Monthly-Sales-Trend.png)
Sales peak in **month 1 (January)** at roughly **₹21.11M**, then decline steadily through mid-year before recovering slightly toward year-end.

### 2. Sales by Category
![Sales by Category](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Sales-by-Category.png)
**Smartphone** leads category revenue at ₹51.64M, ahead of Accessories (₹37.64M), Tablet (₹34.94M), and Laptop (₹28.68M).

### 3. Revenue Contribution by Category
![Revenue Contribution by Category](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Revenue-Contribution-by-Category.png)
Smartphone alone makes up **33.8%** of total revenue, followed by Accessories (24.6%), Tablet (22.9%), and Laptop (18.8%).

### 4. Top 10 Products by Sales
![Top 10 Products by Sales](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Top-10-Products-by-Sales.png)
**Dell Inspiron** is the top-selling product (₹16.38M), closely followed by Laptop Bag (₹16.36M) and Wireless Mouse (₹15.42M).

### 5. Bottom 10 Products by Sales
![Bottom 10 Products by Sales](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Bottom-10-Products-by-Sales.png)
**MacBook Air** trails all products at just ₹1.28M in sales, well behind iPad Air and HP Pavilion.

### 6. Top 10 Customers by Sales
![Top Customers](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Top-Customers.png)
Customer **C0142** is the highest-value customer at ₹1.40M in lifetime purchases, followed by C0218 and C0233.

### 7. Customer Segmentation by Spend
![Customer Segmentation by Spend](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Customer-Segmentation-by-Spend.png)
Customers split into near-equal spend tertiles — Low Value (33.5%), Medium Value (33.2%), High Value (33.2%) — indicating fairly distributed spend rather than concentration in a small elite.

### 8. Repeat vs One-Time Customers
![Repeat vs One-Time Customers](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Repeat-vs-One-Time-Customers.png)
**60.7%** of customers are repeat buyers versus 39.3% one-time — a healthy retention base to build loyalty programs on.

### 9. Sales by City
![Sales by City](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Sales-by-City.png)
**Delhi** dominates with ₹43.11M in sales, more than 1.7× the next city, Mumbai (₹24.58M).

### 10. Customer Distribution by City
![Customer Distribution by City](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Customer-Distribution-by-City.png)
Delhi also holds the largest customer base (131 customers), followed by Mumbai (86) and Bangalore (69).

### 11. Monthly Customer Signup Trend
![Monthly Customer Signup Trend](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Monthly-Customer-Sign-Up-Trend.png)
New-customer signups fluctuate between roughly 7 and 23 per month from Jan 2023 to Jun 2025, without one single dominant growth driver.

### 12. Profitability Analysis (Sales vs Profit)
![Profitability Analysis](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Profitability-Analysis.png)
Higher-value transactions don't consistently mean higher profit — several high-sales transactions still land at or below zero profit, especially at the low end.

### 13. Correlation Analysis
![Correlation Analysis](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Correlation-Analysis.png)
`quantity` and `sales_amount` show a **moderate correlation (r = 0.66)** — order value is influenced by, but not fully explained by, units purchased.

### 14. Sales Amount Outlier Analysis
![Sales Amount Outlier Analysis](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Sales-Amount-Outlier-Analysis.png)
The sales-amount distribution is right-skewed, with a cluster of high-value outlier transactions well beyond the typical IQR range — likely genuine bulk/premium purchases.

---

## 💰 Category Profitability

| Category | Total Sales | Total Profit | Margin |
|---|---:|---:|---:|
| 🟢 Tablet | ₹34,944,107.00 | +₹2,445,401.00 | **+7.00%** |
| 🟢 Accessories | ₹37,639,203.00 | +₹1,505,393.00 | **+4.00%** |
| 🔴 Laptop | ₹28,676,050.50 | −₹925,331.50 | **−3.23%** |
| 🔴 Smartphone | ₹51,643,695.00 | −₹4,750,721.00 | **−9.20%** |

**Key takeaway:** Category profitability is *inverted* relative to revenue — the top-revenue category (Smartphone) is the biggest loss driver, while the smallest-revenue category (Tablet) is the most profitable.

---

## ✅ Business Recommendations

1. **Prioritize profitable growth** — chase margin, not just sales volume.
2. **Fix Smartphone economics first** — audit selling price, base cost, and discounting.
3. **Protect and scale Tablet & Accessories** — the only two profitable categories.
4. **Improve customer retention** — grow the existing 60.7% repeat rate with loyalty programs.
5. **Optimize inventory by demand** — use the January peak and product-level trends to plan stock.
6. **Localize geographic strategy** — concentrate spend on Delhi, Mumbai, and Hyderabad.
7. **Increase basket size** — bundle loss-making Smartphones with profitable Accessories.
8. **Replicate acquisition wins** — study and repeat the drivers behind the strongest signup months.

---

## 🛠 Tech Stack

| Tool | Purpose |
|---|---|
| **Python** | Core programming language |
| **Pandas** | Data cleaning, transformation, and integration |
| **NumPy** | Numerical computation and derived metrics |
| **Matplotlib / Seaborn** | Data visualization |
| **Jupyter Notebook** | Interactive analysis environment |

---

## ▶️ How to Run

```bash
# 1. Clone the repository
git clone https://github.com/mandeepkaur-tech/nexora-sales-analysis.git
cd nexora-sales-analysis

# 2. Install dependencies
pip install pandas numpy matplotlib seaborn jupyter

# 3. Launch the notebook
jupyter notebook notebook/Sales_Analysis_Python_Project.ipynb
```

Make sure to update the 4 csv files paths at the top of the notebook to match your folder structure.

---

## 📄 Documentation

A full formal write-up — Executive Summary, Dataset Information, Data Preparation, Insights & Findings, Recommendations, and Conclusion — is available in [`docs/Project-Documentation.docx`](https://github.com/mandeepkaur-tech/Nexora-Technologies-Sales-Analysis-Python/blob/main/Project-Documentation.docx).

---

## 👤 Author & Contact

**Mandeep Kaur**

- 📧 Email: kaur.mandeeep.08@gmail.com
- 💼 LinkedIn:(https://www.linkedin.com/in/-mandeep-kaur-/)
- 🐙 GitHub:(https://github.com/mandeepkaur-tech)

Feel free to reach out for questions, feedback, or collaboration on this project.

<div align="center">

**NEXORA TECHNOLOGIES · SALES ANALYSIS & BUSINESS INTELLIGENCE**

</div>

# DSA3050-PowerBI-SusanOtieno-670501

# Online Retail Power BI Dashboard

## 1. Project Overview

This project analyses the **Online Retail** transactional dataset using **Microsoft Power BI**. The objective is to transform raw retail transaction data into an interactive dashboard that provides insights into sales performance, customers, transactions, products, returns, and geographical performance.

The project involved:

* Data cleaning and transformation using Power Query
* Data modelling using a star schema
* Creation of DAX measures
* Development of interactive dashboards
* Analysis of sales and customer performance

---

## 2. Dataset

* **Dataset:** Online Retail
* **Source:** UCI Machine Learning Repository
* **Creator:** Daqing Chen
* **Year:** 2015
* **Original Records:** 541,909
* **Period:** 1 December 2010 – 9 December 2011

The dataset contains transactional information including:

* Invoice number
* Stock code
* Product description
* Quantity
* Invoice date
* Unit price
* Customer ID
* Country

The dataset was selected because it supports analysis of sales trends, customer behaviour, product performance, transaction activity, returns, and geographical sales.

---

# 3. Data Preparation and Transformation

The dataset was cleaned and transformed using **Power Query** before being loaded into the Power BI data model.

The main Power Query applied steps included:

* Source
* Promoted Headers
* Changed Type
* Removed Duplicates
* Filtered Rows
* Added Custom columns
* Added Conditional Columns
* Additional filtering
* Inserted Date
* Changed data types
* Capitalized each word

### Key Transformations

**Problem → Transformation → Reason → Result**

### 1. Incorrect column headers

**Transformation:** Promoted the first row as headers.
**Reason:** The original column names needed to be recognised correctly.
**Result:** The dataset had meaningful field names for further transformation.

### 2. Incorrect or inconsistent data types

**Transformation:** Changed data types for relevant columns.
**Reason:** Dates, quantities, prices, and identifiers must have appropriate data types for calculations and relationships.
**Result:** Fields could be used correctly in Power BI calculations and visualisations.

### 3. Duplicate transaction records

**Transformation:** Removed duplicate records.
**Reason:** Duplicate transactions could distort revenue, quantity, and transaction measures.
**Result:** The dataset contained cleaner transaction records.

### 4. Invalid or irrelevant records

**Transformation:** Applied row filters and conditional filters.
**Reason:** Irrelevant records could affect the accuracy of the analysis.
**Result:** The dataset was restricted to records appropriate for analysis.

### 5. Negative quantities

**Transformation:** Negative quantities were retained and classified as return transactions rather than simply deleting them.
**Reason:** Negative quantities represent returned products and contain useful business information.
**Result:** Returns could be analysed separately from normal sales transactions.

### 6. Transaction classification

**Transformation:** Custom and conditional columns were created to classify transaction records.
**Reason:** Different transaction types needed to be distinguished for sales, returns, and cancellation analysis.
**Result:** A `TransactionStatus` field was created for transaction-status analysis.

### 7. Missing Customer IDs

**Transformation:** A cleaned Customer ID field (`CustomerID_Clean`) was created.
**Reason:** Missing customer identifiers could not be used reliably for customer-level analysis.
**Result:** Missing customer IDs were handled consistently and could be excluded from customer rankings where necessary.

### 8. Date preparation

**Transformation:** A Date field was inserted and date attributes were created.
**Reason:** The dataset supports time-based analysis.
**Result:** The data could be analysed by year, month, quarter, and Year-Month.

### 9. Inconsistent product descriptions

**Transformation:** Product descriptions were standardised using capitalisation.
**Reason:** Consistent text improves readability and grouping in visualisations.
**Result:** Product descriptions were presented more consistently.

---

# 4. Data Model

A **star schema** was implemented with `FactSales` as the central fact table and dimension tables providing descriptive information.

## Fact Table

### FactSales

`FactSales` was selected as the main fact table because it contains the transactional records and measurable values required for analysis.

Important fields include:

* Invoice number
* Stock code
* Description
* Quantity
* Unit price
* Revenue
* Customer information
* Country
* Transaction status
* Date information

The table forms the centre of the Power BI model.

## Dimension Tables

### DimProduct

Contains product-related descriptive information such as:

* StockCode
* Description

This dimension supports product-level analysis and filtering.

### DimCustomer

Contains customer-related information used to analyse transactions and revenue by customer.

### DimLocation

Contains geographical information such as Country and supports geographical analysis.

### DimDate

A dedicated Date dimension was created because the dataset contains transaction dates.

It contains attributes such as:

* Date
* Year
* Month
* Month Number
* Quarter
* Year-Month

The Date table supports time-based analysis and monthly revenue trends.

---

# 5. Keys and Relationships

Unique keys were created or identified in the dimension tables so that they could form the **one** side of the relationships.

The main relationships are:

* `DimProduct (1) → FactSales (*)`
* `DimCustomer (1) → FactSales (*)`
* `DimLocation (1) → FactSales (*)`
* `DimDate (1) → FactSales (*)`

All relationships use **one-to-many (1:*) cardinality**.

The cross-filter direction is **Single**, with filtering flowing from the dimension tables to `FactSales`.

This approach was selected to:

* Avoid unnecessary many-to-many relationships
* Reduce ambiguous filter paths
* Avoid unnecessary bidirectional relationships
* Maintain a clear star-schema structure
* Support efficient filtering and analysis

---

# 6. Data Types and Naming

Appropriate data types were assigned to the fields.

Examples include:

* **Date** for date fields
* **Whole Number** for quantities
* **Decimal Number** for prices and revenue
* **Text** for product descriptions, countries, and identifiers where appropriate

Clear naming conventions were also used, including:

* `FactSales`
* `DimProduct`
* `DimCustomer`
* `DimLocation`
* `DimDate`

This makes the model easier to understand and maintain.

---

# 7. Modelling Challenges

Several modelling challenges were encountered during development.

### Duplicate dimension values

Dimension keys must contain unique values to correctly form the **one** side of a one-to-many relationship. Duplicate values were therefore checked and addressed before relationships were created.

### Missing Customer IDs

Some transactions did not contain customer identifiers. These were handled using `CustomerID_Clean`, with `Unknown` excluded from customer-level ranking analysis where an individual customer could not be reliably identified.

### Negative quantities

Negative quantities were retained because they represent returned transactions. Removing them would result in loss of useful return information.

### Relationship design

The model was designed to avoid unnecessary many-to-many and bidirectional relationships. Single-direction, one-to-many relationships were used wherever appropriate.

---

# 8. DAX Measure Documentation

Six important DAX measures were selected for the dashboard.

## 8.1 Total Revenue

```DAX
Total Revenue = SUM(FactSales[Revenue])
```

**What it calculates:** Calculates the total revenue generated from transactions.

**Why it is useful:** Provides the main financial KPI for evaluating overall sales performance.

**Main DAX function:** `SUM()`

**Filter context:** The result changes when filters such as date, country, product, or customer are applied.

**Dashboard use:** Used as a KPI card and in revenue analysis.

---

## 8.2 Total Transactions

```DAX
Total Transactions = DISTINCTCOUNT(FactSales[InvoiceNo])
```

**What it calculates:** Counts the number of unique invoices or transactions.

**Why it is useful:** Measures transaction volume without counting multiple product lines from the same invoice as separate transactions.

**Main DAX function:** `DISTINCTCOUNT()`

**Filter context:** The result changes according to selected dates, countries, products, or customers.

**Dashboard use:** Used as a KPI card and customer/transaction analysis.

---

## 8.3 Total Customers

```DAX
Total Customers = DISTINCTCOUNT(FactSales[CustomerID_Clean])
```

**What it calculates:** Counts the number of unique customers.

**Why it is useful:** Measures the customer base represented in the transaction data.

**Main DAX function:** `DISTINCTCOUNT()`

**Filter context:** The result changes when filters such as date, country, or product are applied.

**Dashboard use:** Used as a KPI card and customer analysis.

---

## 8.4 Average Order Value

```DAX
Average Order Value =
DIVIDE(
    [Total Revenue],
    [Total Transactions],
    0
)
```

**What it calculates:** Calculates the average revenue generated per transaction.

**Why it is useful:** Shows the typical monetary value of a customer order.

**Main DAX function:** `DIVIDE()`

**Filter context:** The measure recalculates according to the current filter context.

**Dashboard use:** Used as a KPI card.

---

## 8.5 Total Returns

```DAX
Total Returns =
CALCULATE(
    [Total Quantity],
    FactSales[TransactionStatus] = "Return"
)
```

**What it calculates:** Calculates the quantity associated with transactions classified as returns.

**Why it is useful:** Helps monitor the volume of returned products.

**Main DAX function:** `CALCULATE()`

**Filter context:** `CALCULATE()` modifies the filter context so that only return transactions are included.

**Dashboard use:** Used as a KPI and for return analysis.

---

## 8.6 Return Rate

```DAX
Return Rate =
DIVIDE(
    ABS([Total Returns]),
    [Total Quantity],
    0
)
```

**What it calculates:** Calculates the proportion of returned quantity relative to total quantity.

**Why it is useful:** Allows return performance to be compared across products, countries, and periods.

**Main DAX functions:** `DIVIDE()` and `ABS()`

**Filter context:** The result changes according to filters applied to the report.

**Dashboard use:** Used for return-performance and diagnostic analysis.

---

# 9. Dashboard Pages

## Page 1 — Executive Sales Overview

The Executive Sales Overview provides a high-level view of overall retail performance.

### KPI Cards

* **Total Revenue:** 9.73M
* **Total Returns:** -207K
* **Total Quantity:** 5M
* **Total Transactions:** 26K
* **Average Order Value:** 375.52
* **Total Customers:** 4K

### Visualisations

**Monthly Revenue Trend**

A line chart using `Year-Month` and `Total Revenue` shows how revenue changes over time.

**Revenue by Country**

A bar chart compares revenue generated across different countries.

**Transaction Status Distribution**

A donut chart shows the distribution of:

* Sale
* Cancelled
* Return

**Top Products by Revenue**

A bar chart highlights the products generating the highest revenue.

### Interactivity

Year and Country slicers allow users to filter the dashboard dynamically.

---

# 10. Page 2 — Customer & Transaction Analysis

The second page provides a deeper analysis of customer behaviour and transaction performance.

### KPI Cards

* **Total Transactions:** 26K
* **Revenue per Customer:** 2.22K
* **Total Customers:** 4K
* **Average Order Value:** 375.52

### Visualisations

**Top 10 Customers by Revenue**

Identifies the customers generating the highest revenue.

`Unknown` customer records were excluded from this ranking because missing customer identifiers cannot reliably represent an individual customer.

**Customers by Country**

Shows the distribution of customers across different countries.

**Top Customers by Transactions**

Identifies customers with the highest transaction activity.

**Customer Performance Details**

A table provides customer-level information including:

* Customer ID
* Country
* Total Transactions
* Revenue per Customer

### Interactivity

Year and Country slicers allow users to analyse customer and transaction performance under different filters.

---

# 11. Dashboard Design and Quality

The dashboard was designed according to the following principles:

### Visual Selection

Visuals were selected based on the questions being answered rather than simply using different chart types.

### Layout

The dashboard uses:

* Clear visual hierarchy
* Consistent spacing
* Alignment of visual elements
* Readable fonts
* Appropriate use of screen space
* Clear titles and labels

### Colour

A consistent colour palette is used across the dashboard to maintain a professional appearance and visual consistency.

### Interactivity

Slicers and Power BI filter context allow users to explore the data dynamically by year, country, customer, and other dimensions.

---

# 12. Dashboard Screenshots

Screenshots of the completed dashboard pages can be included below.

### Page 1 — Executive Sales Overview

*Insert screenshot of Page 1 here.*

### Page 2 — Customer & Transaction Analysis

*Insert screenshot of Page 2 here.*

### Data Model

*Insert screenshot of the completed Power BI Model View here.*

The final screenshot for Page 3 will be added after the Advanced/Diagnostic Analysis page is completed.

---

# 13. Conclusion

The Power BI project transforms the Online Retail dataset into an interactive analytical solution using data preparation, dimensional modelling, DAX measures, and appropriate visualisations.

The first two dashboard pages provide an overall view of sales performance and a deeper analysis of customers and transactions. The final diagnostic page will extend the analysis by investigating the factors behind observed sales performance.

# Retail Data Cleaning and Analysis Project

## Problem Summary
The company exported order-level sales data from multiple internal systems. This raw dataset suffered from severe data quality issues, including inconsistent text formatting, date sequence problems, duplicate records, missing values, invalid financial entries (e.g., negative discounts), and order status anomalies. The objective of this project was to clean the dataset, enforce strict business validation rules, audit all anomalies, and produce a suite of presentation-ready financial summaries for management review.

## Dataset Description
The dataset consists of granular, order-level retail transactions. Key data dimensions include:
* **Logistics & Chronology**: `order_id`, `order_date`, `ship_date`, `ship_mode`
* **Customer Demographics**: `customer_name`, `segment`, `region`, `state`, `city`
* **Product Taxonomy**: `category`, `sub_category`, `product_name`
* **Financials**: `quantity`, `unit_price`, `discount`, `sales`, `cost`, `profit`
* **Fulfillment**: `payment_status`, `order_status`

## Tools Used
* **Microsoft Excel**: Final presentation layer for business users (formatted Tables, Conditional Formatting).

## Cleaning Steps Performed
1. **Text Standardization**: Removed leading, trailing, and double spaces. Converted most text to Proper Case and state codes to UPPERCASE.
2. **Artifact Removal**: Stripped stray internal system symbols (`#`, `*`, `_`) from customer names and product details.
3. **Taxonomy Consolidation**: Standardized misspelled categories (e.g., 'Furn', 'furnitures' → 'Furniture') and statuses via mapping tables.
4. **Calculated Metrics Generation**: Standardized base numbers to compute `calculated_sales`, `calculated_profit`, `profit_margin`, `shipping_delay_days`, and extracted `order_month`/`order_year`.

## Business Rules Applied
* **Discount Validation**: Negative discounts were nullified. Discounts > 1.0 were assumed to be whole percentages (e.g., 15) and corrected to decimals (0.15).
* **Missing Sub-Category Imputation**: Imputed "Phones" for Technology products containing the word "Phone", and "Chairs" for Furniture products containing "Chair".
* **Profitability Tiering**: Flagged records as `Loss` (< 0%), `Low Profit` (0-9.99%), or `High Profit` (>= 10%).
* **Revenue Recognition**: `Cancelled` orders and `Failed` payments were strictly excluded from final realized sales summaries.
* **Returns Tracking**: `Refunded` and `Returned` orders were isolated into a dedicated summary to quantify negative financial impact.

## Summary of Data Quality Issues Found
* **Duplicates**: Found 100% exact duplicate rows (removed) and 'Conflicting' duplicates sharing an `order_id` but containing different row data (flagged for review).
* **Logistical Errors**: Identified records where the `ship_date` preceded the `order_date`.
* **Financial Anomalies**: Found instances of negative discounts and out-of-bounds percentage formats.
* **Missing Data**: Blank sub-categories caused by system sync failures. 
*(Note: All flagged anomalies were tracked in a master `data_quality_flag` column without destroying the original record).*

## Summary of Final Pivot Reports
1. **Region Summary**: Total realized sales and profit by geographic region.
2. **Category Summary**: Hierarchical breakdown of sales/profit by Category and Sub-Category.
3. **Ship Mode Summary**: Count of unique completed orders handled by each shipping tier.
4. **Segment Margin**: Aggregate profit margin percentage broken down by customer segment.
5. **Problem Orders**: Count of refunded, returned, cancelled, or failed orders distributed by region.
6. **Monthly Trend**: Chronological tracking of total valid sales by month and year.

## Key Business Insights
1. **Data Integrity Impact**: The presence of conflicting duplicate `order_id`s indicates a synchronization issue between the POS and fulfillment systems, requiring immediate IT audit to prevent double-counting revenue.
2. **Profitability Margins**: While Top-line sales may be high in certain categories, applying the strict `Profitability Flag` reveals that aggressive discounting is pushing specific sub-categories into the `Loss` or `Low Profit` tiers.
3. **Logistics Bottlenecks**: A noticeable volume of orders were flagged for logistical impossibilities (Ship Date < Order Date), suggesting manual data entry errors at the warehouse level.
4. **Return Rates**: By isolating the Problem Orders pivot, management can now clearly see which regions drive the highest volume of Returns/Refunds, allowing for targeted quality control.

## Assumptions and Limitations
* **Assumptions**: 
  * Exact duplicate rows are pure system glitches and safe to permanently delete.
  * Erroneous discounts over 1.0 (e.g., 20) were intended as 20% (0.20).
  * Missing quantitative metrics (sales, cost) were defaulted to 0 to prevent downstream pipeline crashes.
* **Limitations**: 
  * Keyword-based imputation (e.g., searching for "Chair") is a heuristic and may misclassify edge-case products.
  * Automated scripts cannot independently resolve *which* conflicting duplicate is the ground truth; human intervention is required for final resolution.

## Screenshots Included

![Raw Data Issues]
*Figure 1: Examples of inconsistent text, stray characters, and missing values in the raw export.*

![Cleaned Data preview]
*Figure 2: Examples of cleaned columns, calculated columns, & removed discrapancies*

![Pivot Summaries]
*Figure 3: Final realization of clean, formatted pivot tables for business review.*

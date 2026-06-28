*TASK 5 - BUSINESS RULES & DATA PROCESSING DOCUMENTATION
==============================================

1. DISCOUNT VALIDATION
----------------------
- Rule: Discounts cannot be negative.
  Action: Created a 'Discount_Validation_Flag'. Any discount value < 0 was explicitly flagged as "Invalid - Negative Discount".
- Rule: Discounts cannot exceed the allowable limit (100% or 1.0).
  Action: Any discount value > 1.0 was flagged as "Invalid - Discount Above Allowed Range".
- Result: Valid discounts are marked as "Valid", allowing for easy filtering of data-entry anomalies without destroying the original record.

2. LOGISTICAL VALIDATION (DATE INTEGRITY)
-----------------------------------------
- Rule: An order cannot be shipped before it is placed.
  Action: Compared 'order_date' and 'ship_date'.
- Result: Any record where ship_date < order_date was flagged as "Invalid - Ship Date Earlier Than Order Date" in a new 'Date_Validation_Flag_Final' column.

3. SUMMARY REPORTING RULES (COMPLETED SALES)
--------------------------------------------
- Rule: Cancelled orders do not represent realized revenue and must be excluded.
- Rule: Failed payments represent unrealized transactions and must be excluded.
- Action: Created the 'Completed_Sales_Summary' tab by completely filtering out any row where 'order_status' = Cancelled OR 'payment_status' = Failed.

4. SUMMARY REPORTING RULES (REFUNDS & RETURNS)
----------------------------------------------
- Rule: Refunded and returned orders require a separate audit view to assess impact.
- Action: Created the 'Refunded_Orders_Summary' tab by filtering ONLY for rows where 'order_status' = Refunded or Returned. This tab calculates the lost sales volume, lost profit, and number of affected orders by category.

Note: Invalid rows were securely flagged in the master 'Processed_Data' tab rather than being deleted, preserving the audit trail for data correction teams.


*TASK 9 - COMPREHENSIVE DATA CLEANING & PROCESSING LOG


1. LIST OF ISSUES FOUND
-----------------------
- Text Formatting: Extra leading/trailing/double spaces, inconsistent casing, and non-printable system artifacts.
- Special Characters: Stray symbols (#, *, _) appended to text fields by internal systems.
- Categorical Misalignment: Fragmented and misspelled product categories (e.g., 'Furn', 'furnitures') and statuses (e.g., 'success', 'declined').
- Duplicates: Exact duplicate rows across all columns, and conflicting duplicate rows sharing the same 'order_id' but containing different information.
- Missing Data: Blank 'sub_category' fields for certain products.
- Invalid Discounts: Negative discount values and discount values exceeding 100% (or 1.0).
- Logistical Errors: Records where the 'ship_date' preceded the 'order_date'.

2. CLEANING ACTIONS PERFORMED
-----------------------------
- Text Standardization: Applied TRIM and regex space removal. Applied PROPER casing to most fields and UPPER casing to State codes. Stripped special characters using substitution functions.
- Taxonomy Consolidation: Utilized mapping dictionaries to enforce standard Categories ('Furniture', 'Office Supplies', 'Technology') and Statuses ('Paid', 'Failed', 'Pending', 'Shipped', 'Delivered', 'Processing').
- Column Generation: Created standardized financial and operational metrics including 'cleaned_discount', 'calculated_sales', 'calculated_profit', 'profit_margin', 'shipping_delay_days', 'order_month', and 'order_year'.

3. BUSINESS RULES APPLIED
-------------------------
- Missing Sub-Category Imputation: 
  * Tech products containing "Phone" -> 'Phones'
  * Furniture products containing "Chair" -> 'Chairs'
- Profitability Flagging: 
  * Profit Margin < 0% -> 'Loss'
  * 0% <= Profit Margin < 10% -> 'Low Profit'
  * Profit Margin >= 10% -> 'High Profit'
- Summary Inclusions/Exclusions: 
  * Cancelled orders and Failed payments strictly excluded from realized/completed revenue summaries.
  * Refunded and Returned orders strictly isolated to quantify negative financial impact.
- Discount Rules: Standardized discounts > 1.0 by dividing by 100 (assuming whole percentage entry). Set negative discounts to 0 for calculation purposes.

4. ASSUMPTIONS MADE
-------------------
- Discount Scale: Assumed discounts entered greater than 1 (e.g., 15) were intended as whole percentages (15%) and automatically corrected them to decimal format (0.15) for calculations.
- Missing Financials: Assumed missing numeric values for quantity, unit_price, cost, and discount should default to 0 to prevent downstream calculation crashes.
- Duplicate Priority: Assumed for exact duplicates that the first occurrence is the valid record and subsequent identical rows are system export glitches.

5. RECORDS REMOVED
------------------
- Exact Duplicates: Completely identical rows were removed from the active dataset. (Logged in a separate audit file during the duplication phase).

6. RECORDS FLAGGED (NOT REMOVED)
--------------------------------
- Conflicting Duplicates: Flagged as 'Warning' ("Conflicting Duplicate - Review Required").
- Invalid Dates: Records where Ship Date < Order Date were flagged as 'Invalid'.
- Invalid Discounts: Negative discounts were flagged as 'Invalid - Negative Discount'. Discounts > 1.0 were initially flagged as 'Invalid - Above Allowed Range' (and later corrected in the calculated metrics).
- Master Quality Flag: A consolidated 'data_quality_flag' was appended indicating 'Clean', 'Warning', or 'Invalid' for fast evaluator filtering.

7. LIMITATIONS OF THE CLEANING PROCESS
--------------------------------------
- Conflicting Duplicates Resolution: The automated process cannot determine which version of a conflicting 'order_id' is the Ground Truth. Manual stakeholder review is still required for these flagged items.
- Imputation Scope: Imputing sub-categories based solely on strings like "Phone" or "Chair" may miscategorize edge cases (e.g., "Dollhouse Chair" misclassified as actual Furniture).
- Date Imputation: Missing 'order_date' or 'ship_date' values were left as 'Missing' because accurate dates cannot be mathematically inferred without domain context or secondary system lookups.

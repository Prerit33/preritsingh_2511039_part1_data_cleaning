BUSINESS RULES & DATA PROCESSING DOCUMENTATION

1. TEXT STANDARDIZATION & CLEANING RULES
----------------------------------------
- Spacing & Non-Printable Characters: Leading, trailing, and double spaces were removed using TRIM/regex equivalents. Non-printable system artifacts were purged.
- Special Characters: Stray symbols (such as '#', '*', '_') appended by internal systems were stripped from all text fields (Customer Name, Category, etc.).
- Casing: 
  * 'State' codes were standardized to UPPERCASE.
  * All other text fields (Customer Name, Segment, Region, City, Sub-Category, Ship Mode) were standardized to Proper Title Case.

2. CATEGORY & STATUS MAPPING RULES
----------------------------------
- Product Categories: Consolidated fragmented and misspelled categories into a strict taxonomy:
  * 'Furn', 'furnitures', 'furn' -> 'Furniture'
  * 'Office Sup', 'office sup' -> 'Office Supplies'
  * 'Tech', 'tech' -> 'Technology'
- Payment Status: 
  * 'success' -> 'Paid'
  * 'declined' -> 'Failed'
  * 'Pending' -> 'Pending'
- Order Status: 
  * 'shipped' -> 'Shipped'
  * 'processing' -> 'Processing'
  * 'Delivered' -> 'Delivered'

3. DUPLICATE HANDLING RULES
---------------------------
- Exact Duplicates: Rows where every single column perfectly matched another row were classified as system export glitches. The first instance was retained, and all redundant copies were removed entirely from the active dataset.
- Conflicting Duplicates: Rows sharing the same 'order_id' but containing conflicting information in other columns (e.g., different ship dates or product names) were NOT deleted. 
  * Action: These were retained and explicitly marked with "Conflicting Duplicate - Review Required" in a new 'Duplicate_Status' column for manual stakeholder review.

4. MISSING DATA IMPUTATION (BUSINESS RULES)
-------------------------------------------
- Sub-Category Backfilling: 
  * Rule 1: If Category is 'Technology' and Product Name contains the word 'Phone' (case-insensitive), the missing/incorrect Sub-Category is forced to 'Phones'.
  * Rule 2: If Category is 'Furniture' and Product Name contains the word 'Chair' (case-insensitive), the missing/incorrect Sub-Category is forced to 'Chairs'.

5. FINANCIAL CALCULATIONS & FLAG LOGIC
--------------------------------------
- Profit Margin Calculation: 
  * Formula: (Profit / Sales) * 100
  * Rounding: Strictly rounded to 2 decimal places.
  * Exception Handling: If Sales = 0 or is missing, the margin defaults to 0% to prevent division errors.
- Profitability Tiering (Flagging):
  * 'Loss': Assigned if Profit Margin is less than 0%.
  * 'Low Profit': Assigned if Profit Margin is between 0.00% and 9.99%.
  * 'High Profit': Assigned if Profit Margin is 10.00% or greater.

BUSINESS RULES & DATA PROCESSING DOCUMENTATION
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

# Accounts Payable & Vendor Analytics SQL Project

## Project Overview
This project demonstrates SQL-based Accounts Payable (AP) and Vendor Analytics using a sample vendor payments dataset.

### Business Objectives
- Analyze vendor spend
- Track outstanding invoices
- Monitor aging buckets
- Evaluate analyst workload
- Measure payment performance
- Identify vendor concentration risks
- Support AP dashboard reporting

---

# Data Model

```sql
CREATE TABLE Vendor_Payments (
    Vendor_Company_Code VARCHAR(20),
    Company_Name VARCHAR(100),
    Payee_Num VARCHAR(20),
    Vendor_Code VARCHAR(20),
    Vendor_Name VARCHAR(100),
    Vendor_Type VARCHAR(50),
    Organization VARCHAR(100),
    Vendor_Country VARCHAR(50),
    Region VARCHAR(50),
    Analyst VARCHAR(100),
    Age INT,
    Net_Amount DECIMAL(18,2),
    Aging VARCHAR(20),
    Channel VARCHAR(50),
    Invoice_Date DATE,
    Approved_Amt DECIMAL(18,2),
    Paid_Date DATE
);
```

---

# Key Business Queries

## 1. Total Spend by Vendor

```sql
SELECT Vendor_Name,
       SUM(Net_Amount) AS Total_Spend
FROM Vendor_Payments
GROUP BY Vendor_Name
ORDER BY Total_Spend DESC;
```

## 2. Outstanding Invoices

```sql
SELECT *
FROM Vendor_Payments
WHERE Paid_Date IS NULL;
```

## 3. Aging Bucket Analysis

```sql
SELECT Aging,
       COUNT(*) AS Invoice_Count,
       SUM(Net_Amount) AS Total_Amount
FROM Vendor_Payments
GROUP BY Aging;
```

## 4. Spend by Region

```sql
SELECT Region,
       SUM(Net_Amount) AS Total_Spend
FROM Vendor_Payments
GROUP BY Region;
```

## 5. Top Vendors by Spend

```sql
SELECT Vendor_Name,
       SUM(Net_Amount) AS Total_Spend
FROM Vendor_Payments
GROUP BY Vendor_Name
ORDER BY Total_Spend DESC;
```

## 6. Analyst Performance

```sql
SELECT Analyst,
       COUNT(*) AS Invoice_Count,
       SUM(Net_Amount) AS Invoice_Value,
       AVG(Age) AS Avg_Aging
FROM Vendor_Payments
GROUP BY Analyst;
```

## 7. Vendor Type Spend Analysis

```sql
SELECT Vendor_Type,
       SUM(Net_Amount) AS Total_Spend
FROM Vendor_Payments
GROUP BY Vendor_Type;
```

## 8. Payment Cycle Time

```sql
SELECT Vendor_Name,
       Invoice_Date,
       Paid_Date,
       DATEDIFF(day, Invoice_Date, Paid_Date) AS Payment_Days
FROM Vendor_Payments
WHERE Paid_Date IS NOT NULL;
```

## 9. Rank Vendors by Spend

```sql
SELECT Vendor_Name,
       SUM(Net_Amount) AS Total_Spend,
       RANK() OVER (
           ORDER BY SUM(Net_Amount) DESC
       ) AS Vendor_Rank
FROM Vendor_Payments
GROUP BY Vendor_Name;
```

## 10. Outstanding Amount by Aging Bucket

```sql
SELECT Aging,
       SUM(Net_Amount) AS Outstanding_Amount
FROM Vendor_Payments
WHERE Paid_Date IS NULL
GROUP BY Aging;
```

## 11. Vendor Concentration Risk

```sql
SELECT Vendor_Name,
       SUM(Net_Amount) AS Spend,
       ROUND(
           SUM(Net_Amount) * 100.0
           / SUM(SUM(Net_Amount)) OVER(),
           2
       ) AS Spend_Percentage
FROM Vendor_Payments
GROUP BY Vendor_Name
ORDER BY Spend_Percentage DESC;
```

## 12. SLA Compliance

```sql
SELECT Vendor_Name,
       DATEDIFF(day, Invoice_Date, Paid_Date) AS Days_To_Pay,
       CASE
           WHEN DATEDIFF(day, Invoice_Date, Paid_Date) <= 60
           THEN 'Within SLA'
           ELSE 'SLA Breached'
       END AS SLA_Status
FROM Vendor_Payments
WHERE Paid_Date IS NOT NULL;
```

## 13. Duplicate Payment Detection

```sql
SELECT Vendor_Name,
       Invoice_Date,
       Net_Amount,
       COUNT(*) AS Duplicate_Count
FROM Vendor_Payments
GROUP BY Vendor_Name,
         Invoice_Date,
         Net_Amount
HAVING COUNT(*) > 1;
```

## 14. Top Vendor by Region

```sql
WITH VendorSpend AS (
    SELECT Region,
           Vendor_Name,
           SUM(Net_Amount) AS Spend,
           RANK() OVER(
               PARTITION BY Region
               ORDER BY SUM(Net_Amount) DESC
           ) AS Rank_No
    FROM Vendor_Payments
    GROUP BY Region, Vendor_Name
)
SELECT *
FROM VendorSpend
WHERE Rank_No = 1;
```

## 15. Executive Dashboard Metrics

```sql
SELECT
    COUNT(*) AS Total_Invoices,
    COUNT(CASE WHEN Paid_Date IS NULL THEN 1 END) AS Open_Invoices,
    SUM(Net_Amount) AS Total_Spend,
    SUM(CASE WHEN Paid_Date IS NULL
             THEN Net_Amount ELSE 0 END) AS Outstanding_Amount,
    AVG(Age) AS Average_Aging
FROM Vendor_Payments;
```

---

# Skills Demonstrated

- SQL Joins
- Aggregations
- Window Functions
- CTEs
- Ranking Functions
- Aging Analysis
- Accounts Payable Analytics
- Vendor Risk Analysis
- SLA Reporting
- Executive Dashboard Metrics

---

# Tools Used

- SQL Server / PostgreSQL
- Tableau
- Excel
- GitHub

---

# Future Enhancements

- Power BI Dashboard
- Tableau Dashboard
- Forecasting Vendor Payments
- Payment Delay Prediction
- AP KPI Monitoring

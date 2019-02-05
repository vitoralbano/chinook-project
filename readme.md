# Project Chinook
# Questions:
1. Questions set 01
    1. [Which countries have more invoices?](##-1.1.-Which-countries-have-more-invoices)
    2. [Which cities have the bestest clients?](##-1.2.-Which-cities-have-the-bestest-clients)
    3. [Who is the bestest client?](##-1.3.-Who-is-the-bestest-client?)

# Entity Relationship Diagram
![Chinook ERD](db/chinook-erd.png)



# Answers
## 1.1. Which countries have more invoices?
```sql
SELECT 
    BillingCountry,
    COUNT(*) Invoices
FROM Invoice
    GROUP BY BillingCountry
ORDER BY Invoices DESC;
```
Result:
| BillingCountry   | Invoices |
|------------------|----------|
| "USA"            | "91"     |
| "Canada"         | "56"     |
| "Brazil"         | "35"     |
| "France"         | "35"     |
| "Germany"        | "28"     |
| "United Kingdom" | "21"     |
| "Czech Republic" | "14"     |
| "Portugal"       | "14"     |
| "India"          | "13"     |
| "Argentina"      | "7"      |
| "Australia"      | "7"      |
| "Austria"        | "7"      |
| "Belgium"        | "7"      |
| "Chile"          | "7"      |
| "Denmark"        | "7"      |
| "Finland"        | "7"      |
| "Hungary"        | "7"      |
| "Ireland"        | "7"      |
| "Italy"          | "7"      |
| "Netherlands"    | "7"      |
| "Norway"         | "7"      |
| "Poland"         | "7"      |
| "Spain"          | "7"      |
| "Sweden"         | "7"      |



## 1.2. Which cities have the bestest clients?

Query:
```sql
SELECT Inv.BillingCity,
    MAX(Inv.Total) MaxInvoice
FROM 
    (SELECT BillingCity,
        SUM(Total) Total
	FROM Invoice
    GROUP BY BillingCity
    ORDER BY Total) Inv
```
Result:
| BillingCity | MaxInvoice |
|-------------|------------|
| "Prague"    | "90.24"    |

## 1.3. Who is the bestest client?

Query:
```sql
SELECT 
    Cus.CustomerId,
    Cus.FirstName,
    Cus.Country,
    Cus_Profit.Total TotalProfit
FROM 
    (SELECT 
        Inv.CustomerId,
        SUM(Inv.Total) Total
    FROM Invoice Inv
    GROUP BY Inv.CustomerId
    ORDER BY 2 DESC LIMIT 1) Cus_Profit
JOIN Customer Cus ON Cus.CustomerId = Cus_Profit.CustomerId;
```
Result:
| CustomerId | FirstName | Country          | TotalProfit |
|------------|-----------|------------------|-------------|
| "6"        | "Helena"  | "Czech Republic" | "49.62"     |
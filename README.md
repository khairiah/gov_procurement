Data Crawler Project

# Extract
- Utilized government procurement & ACRA dataset from data.gov.sg

# Transform
- Normalize text (e.g., agency names, supplier names).
- Standardize date formats.
- Clean categorical variables.
- Derive new columns where useful.

# Load
- Designed a star schema with fact and dimensions tables
- Loaded data into PostgreSQL using SQLAlchemy.

# Future Enhancements
- Load DataFrame to Microsoft Azure SQL Database
- Train text classifier for `procurement_description` column
- Use tools like Airflow for scheduling & orchestration
- Data quality testing with PyTest or Great Expectations

# Basic database schema (for procurement dataset only)

- `dim_agency(agency_id, agency_name, agency_sub)`  
- `dim_supplier(supplier_id, supplier_name)`  
- `dim_tender(tender_id, tender_no, description, year)`  
- `dim_status(status_id, status) ` 
- `fact_tender_supplier(fact_id, tender_id, supplier_id, status_id, awarded_amt)`

# Sample Queries

```
SELECT COUNT(*) 
FROM fact_tender_supplier f
JOIN dim_tender t ON f.tender_id = t.tender_id
WHERE t.year = 2024;

# Top 5 suppliers by awarded amount
SELECT s.supplier_name, SUM(f.awarded_amt) AS total_awarded
FROM fact_tender_supplier f
JOIN dim_supplier s ON f.supplier_id = s.supplier_id
GROUP BY s.supplier_name
ORDER BY total_awarded DESC
LIMIT 5;
```

# Sample Chart

## Top 10 Suppliers in 2024
```
query = """
SELECT s.supplier_name, SUM(f.awarded_amt) AS total_awarded
FROM fact_awards f
JOIN dim_supplier s ON f.supplier_id = s.supplier_id
WHERE f.award_date BETWEEN '2024-01-01' AND '2024-12-31'
GROUP BY s.supplier_name
ORDER BY total_awarded DESC
LIMIT 10;
"""
df_suppliers_2024 = pd.read_sql(text(query), engine)

# plot
df_suppliers_2024 = df_suppliers_2024.sort_values("total_awarded", ascending=True)  # small → large

plt.figure(figsize=(10,6))
plt.barh(df_suppliers_2024['supplier_name'], df_suppliers_2024['total_awarded'])
plt.title("Top 10 Suppliers by Awarded Amount (2024)")
plt.xlabel("Total Awarded ($ Billions)")
plt.ylabel("Supplier")
plt.show()
```

<img width="1097" height="481" alt="image" src="https://github.com/user-attachments/assets/f33bc0e1-df56-48f2-aa77-54375ce9f935" />

# Setup Instructions
- Create new `.env` file and add in your API key
- Update PostgreSQL login details and port

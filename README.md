ETL Practice Project

# Extract
- Download dataset via API

# Transform
- Normalize text (e.g., agency names, supplier names).
- Standardize date formats.
- Clean categorical variables.
- Derive new columns where useful.

# Load
- Designed a relational schema:
- Dimension tables (e.g., agency, supplier, tender status).
- Fact table (linking dimensions with metrics such as awarded amount).
- Loaded data into PostgreSQL using SQLAlchemy.

# Incomplete
- [ ] Data cleaning. Extra focus needed on cleaning `agency` and `supplier_name` columns. 
- [ ] Validation check for creation of database in PostgreSQL. If exist, don't need to recreate again.
- [ ] Improve database design. Utilized a basic star schema. Can be normalized further or possibly use a snowflake schema.
- [ ] Data quality testing with PyTest or Great Expectations
- [ ] Integration testing

# Future Enhancements
- Load DataFrame to Microsoft Azure SQL Database
- Train text classifier for `procurement_description` column
- Create a word cloud
- Use tool like Airflow for scheduling & orchestration
- Data quality testing with PyTest or Great Expectations

---

# Basic database schema

- `dim_agency(agency_id, agency_name, agency_sub)`  
- `dim_supplier(supplier_id, supplier_name)`  
- `dim_tender(tender_id, tender_no, description, year)`  
- `dim_status(status_id, status) ` 
- `fact_tender_supplier(fact_id, tender_id, supplier_id, status_id, awarded_amt)`

# Sample Queries

`Total tenders in 2024`
SELECT COUNT(*) 
FROM fact_tender_supplier f
JOIN dim_tender t ON f.tender_id = t.tender_id
WHERE t.year = 2024;

`Top 5 suppliers by awarded amount`
SELECT s.supplier_name, SUM(f.awarded_amt) AS total_awarded
FROM fact_tender_supplier f
JOIN dim_supplier s ON f.supplier_id = s.supplier_id
GROUP BY s.supplier_name
ORDER BY total_awarded DESC
LIMIT 5;

# Sample Chart
## Top 10 agencies by total amount procured spent
```
query = """
SELECT a.agency, SUM(f.awarded_amt) AS total_awarded
FROM fact_awards f
JOIN dim_agency a ON f.agency_id = a.agency_id
WHERE f.award_date BETWEEN '2024-01-01' AND '2024-12-31'
GROUP BY a.agency
ORDER BY total_awarded DESC
LIMIT 10;
"""
df_agency_2024 = pd.read_sql(text(query), engine)

# plot
plt.figure(figsize=(12,6))
plt.bar(df_agency_2024['agency'], df_agency_2024['total_awarded'])
plt.title("Top 10 Agencies by Total Awards (2024)")
plt.xlabel("Agency")
plt.ylabel("Total Awarded ($)")
plt.xticks(rotation=45, ha="right")
plt.show()
```

<img width="977" height="652" alt="image" src="https://github.com/user-attachments/assets/e3f3cca5-bac2-4f5c-b132-866c1e860489" />



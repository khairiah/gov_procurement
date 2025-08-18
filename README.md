ETL Practice Project

Extract
- Download data from API
- Handle missing values and invalid entries.

Transform
- Normalize text (e.g., agency names, supplier names).
- Standardize date formats.
- Clean categorical variables.
- Derive new columns where useful.

Load
- Designed a relational schema:
- Dimension tables (e.g., agency, supplier, tender status).
- Fact table (linking dimensions with metrics such as awarded amount).
- Loaded data into PostgreSQL using SQLAlchemy.

Incomplete
- [ ] Data cleaning. Extra focus needed on cleaning `agency` and `supplier_name` columns
- [ ] Validation check for creation of database in PostgreSQL. If exist, don't need to recreate again.
- [ ] Improve basic star schema. Can be normalized further. 

Future Enhancements
- Train text classifier for procurement_description column
- Use tool like Airflow for scheduling & orchestration

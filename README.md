
# Pacbook Case




## 1. Requirements Gathering & Proposed Solution

- Source of the Data
The data originates from PacBook's relational database schema, containing tables like customer, order_line, book, author, and others, reflecting sales transactions and product details.

- Proposed Solutions
To address PacBook's need for a dedicated storage solution for sales analysis, we propose building a Data Warehouse using a Dimensional Model. This separates operational data from analytical data, enabling efficient analysis without impacting live systems.

## 2. Design Dimensional Model Process

- Select Business Process:
The chosen business process is Order Transaction, focusing on customer purchases of books.

- Declare Grain:
We will utilize two fact tables with different grains:
```bash
Grain 1: Order Line Item: Represents a single purchased product by a book (granular).
Grain 2: Order Line Item: Represents a single purchased product by a country (granular).
Grain 3: Daily Sales: Represents the daily total sales for each product across all orders (less granular).
```

c. Identify the Dimensions:

The following dimensions will be included:
```bash
dim_book: Stores book information (ID, title, ISBN, language, publication date, author information, publisher information, etc.).
dim_country: Captures book details (ID, street number,street name,country name etc.).
dim_order: Provides calendar-related information (date, day of week, month, year, etc.).
```
d. Identify the Facts:

Two fact tables will be created:
- fct_order: Contains measures related to individual order line items, such as:
```bash
order_line_id (foreign key to order_line table)
customer_id (foreign key to dim_customer)
product_id (foreign key to dim_product)
quantity_ordered
price
discount_amount (optional)
order_date (foreign key to dim_date)
```
- fct_daily_sales: Focuses on daily sales for each product, including:
```bash
date_id (foreign key to dim_date)
product_id (foreign key to dim_product)
total_sales
number_of_orders
etc.
```
- Slowly Changing Dimension (SCD) Strategy:
A Type 2 SCD strategy will be implemented for the dim_customer table to handle customer information changes (e.g., address updates). This involves creating a new record with updated information while maintaining the old record with historical data. Additional columns like "update_date" and "valid_from"/"valid_to" can be added to track changes.

## 3. Data Warehouse Diagram
![App Screenshot](https://github.com/sikudahebatjr/pacbook/blob/main/diagram_kuda.jpg?raw=true)

## 4. Design of the ETL Pipeline
### Stack or Tools or Libraries Used:
- Python for data extraction and interacting with databases.
- Luigi: A Python framework for building scalable and maintainable data pipelines.
- dbt: A data transformation tool designed for working with data warehouses.

### Design of the ETL Pipeline
The pipeline follows an ELT approach:
- Extract: A Python script extracts relevant tables from the source database using libraries like psycopg2 (PostgreSQL) or pyodbc (SQL Server).
- Load to Staging: Extracted data is loaded into a designated staging area within the Data Warehouse database using libraries like pandas and bulk loading techniques.
- Transform: dbt models (*.sql) define transformations on the staging data:
```bash
Data cleaning (handling missing values, inconsistencies)
Deriving new attributes (e.g., total order value)
Dimension table updates (implementing SCD for dim_customer)
```
- Load to Data Warehouse: The transformed data is loaded into the final Data Warehouse tables defined by the dimensional model.

## 5. How the ETL Pipeline Works and How to Run It
- Luigi Tasks: The pipeline is implemented as Luigi tasks (Python classes). Each task handles a specific step (extraction, transformation, loading).
- Scheduling: Luigi tasks can be scheduled to run periodically using cron jobs or cloud-based schedulers.

## Output Results from Each Scenario

On file Pacbook_case.ipynb


# E-Trgovina database

This repository contains the SQL scripts and schema for the **E-Trgovina** (E-Commerce) database system. The database is designed to manage an e-commerce platform, including customers, products, orders, deliveries, payments, and reviews, with full relational integrity and data validation.  

## Database overview

The database is implemented in **Microsoft SQL Server** and follows a relational model. Key features include:  

- **Relational integrity:** Foreign key constraints ensure proper relationships between tables and maintain data consistency.  
- **Cascading options:** Many tables use `ON DELETE CASCADE` or `ON DELETE SET NULL` to automatically manage dependent records.  
- **Data validation:** Columns use `CHECK` constraints, `UNIQUE`, and `NOT NULL` constraints to enforce business rules.  
- **Data types:** Appropriate SQL data types (`nvarchar`, `decimal`, `datetime`, `varbinary`) are used for flexibility and precision.  
- **Default values:** Certain columns, such as `DatumNarudzbe` and `DatumRecenzije`, are pre-defined with default timestamps.  

## Core tables and relationships

### Customers and staff
- **Kupci** – Stores customer details with unique JMBG and Email.  
- **Zaposlenici** – Stores employee data, including optional profile pictures.  

### Products and manufacturers
- **Proizvodjaci** – Stores manufacturer details.  
- **VrsteProizvoda** – Stores product categories.  
- **Proizvodi** – Stores product details, pricing, stock, and optional images. Links to manufacturers and categories.  

### Orders and payments
- **Narudzbe** – Orders placed by customers, processed by staff, with status validation.  
- **StavkeNarudzbe** – Order items with quantity and discount validation.  
- **Placanja** – Payment information including amount, date, and method validation.  

### Delivery and addresses
- **Dostave** – Delivery details for orders with statuses and timestamps.  
- **Adrese** – Address details linking to customers, employees, manufacturers, deliveries, and cities.  
- **Gradovi** – Cities linked to countries.  
- **Drzave** – Countries with unique codes, calling codes, and continent information.  

### Reviews
- **Recenzije** – Customers can review products with ratings (1–5) and optional comments.  

### Relationships
- **Customers → Orders → Order items → Products**  
- **Orders → Payments / Deliveries**  
- **Products → Manufacturers & Categories**  
- **Addresses → Customers / Employees / Manufacturers / Deliveries**  
- **Cities → Countries**  

This relational setup ensures a normalized structure while enabling detailed queries for reporting, analytics, and operational processes.  

## Data seed and conversions

- Approximately **200 test records** have been added to populate the database for testing and demonstration purposes.  
- **Conversions and rounding** were applied for decimal fields such as `Cijena` (Price) and `Popust` (Discount) to ensure precision.  
- Predefined sets for columns like `StatusDostave`, `Status` (orders), `NacinPlacanja`, and ratings (`Ocjena`) are enforced in the database.  

## Usage

1. Clone this repository.  
2. Open `SSMS` or another SQL Server interface.  
3. Execute `db_schema.sql` to create the `E-trgovina` database and all tables.  
4. Run the data script to populate test data.  
5. Use the database for queries, reporting, or further development.  

## Notes

- All relationships are defined to maintain referential integrity.  
- Cascading deletes and nullification are used to ensure smooth data maintenance without orphaned records.  
- The database is suitable for learning, reporting (SSRS/SSDT), and small-scale e-commerce projects.  

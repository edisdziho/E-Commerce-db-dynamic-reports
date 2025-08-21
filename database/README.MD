# E-Trgovina Database

This repository contains the SQL scripts and schema for the **E-Trgovina (E-Commerce)** database system. The database is designed to manage a full e-commerce platform, including customers, products, orders, deliveries, payments, and reviews, with full relational integrity and data validation.

---

## Database overview

The E-Trgovina database is implemented in **Microsoft SQL Server** and follows a relational model. It is designed to handle the core business processes of an e-commerce system efficiently and with data integrity.

Key features include:

- **Relational integrity:** Foreign key constraints ensure proper relationships between tables and maintain data consistency.  
- **Cascading options:** Many tables use `ON DELETE CASCADE` or `ON DELETE SET NULL` to automatically manage dependent records.  
- **Data validation:** Columns use `CHECK`, `UNIQUE`, and `NOT NULL` constraints to enforce business rules.  
- **Appropriate data types:** `nvarchar`, `decimal`, `datetime`, `varbinary` are used for flexibility and precision.  
- **Default values:** Certain columns, such as `DatumNarudzbe` and `DatumRecenzije`, are pre-defined with default timestamps.  

---

## Tables and relationships

### Customers and staff
- **Customers (`Kupci`)** – Stores customer details. Unique constraints are applied on `JMBG` and `Email`.  
  **Attributes:** `KupacID` (PK), `Ime`, `Prezime`, `Adresa`, `JMBG` (UNIQUE), `BrojTelefona`, `Email` (UNIQUE)  
  **Constraints & validation:** `JMBG` and `Email` must be unique.  
  **Purpose:** Tracks individual customers and allows linking to orders and reviews. This table represents the people who buy products from the platform.

- **Employees (`Zaposlenici`)** – Stores employee data including optional profile pictures. Unique constraints are applied on `JMBG` and `Email`.  
  **Attributes:** `ZaposlenikID` (PK), `Ime`, `Prezime`, `Adresa`, `JMBG` (UNIQUE), `BrojTelefona`, `Email` (UNIQUE), `DatumRodjenja`, `DatumZaposlenja`, `DatumOtkaza`, `Slika`  
  **Constraints & validation:** `JMBG` and `Email` must be unique.  
  **Purpose:** Represents staff members who process orders, manage deliveries, and handle payments.

---

## Products and manufacturers
- **Manufacturers (`Proizvodjaci`)** – Stores manufacturer details with a unique `Email`.  
  **Attributes:** `ProizvodjacID` (PK), `Naziv`, `Adresa`, `Email` (UNIQUE), `BrojTelefona`  
  **Constraints:** `Email` must be unique.  
  **Purpose:** Represents companies that produce products sold on the platform.

- **Product categories (`VrsteProizvoda`)** – Stores categories for products.  
  **Attributes:** `VrstaProizvodaID` (PK), `Naziv`  
  **Purpose:** Helps organize products into logical groups for easier browsing and reporting.

- **Products (`Proizvodi`)** – Stores product details, including price, stock, and optional images. Links to manufacturers and categories. On deletion of a manufacturer or category, the corresponding field is set to `NULL`.  
  **Attributes:** `ProizvodID` (PK), `Naziv`, `Cijena`, `NaStanju`, `DatumProizvodnje`, `ProizvodjacID` (FK), `VrstaProizvodaID` (FK), `Slika`  
  **Constraints & validation:**  
  - `Cijena` must be a positive number  
  - `NaStanju` (stock) cannot be negative  
  **Purpose:** Represents the products available for sale on the platform, including stock and pricing information.

---

## Orders and payments
- **Orders (`Narudzbe`)** – Tracks orders placed by customers and processed by staff.  
  **Attributes:** `NarudzbaID` (PK), `KupacID` (FK), `ZaposlenikID` (FK), `DatumNarudzbe`, `Status`  
  **Constraints & validation:**  
  - Deleting a customer cascades to their orders  
  - Deleting a staff member sets `ZaposlenikID` to `NULL`  
  - `Status` must be one of five predefined values  
  **Purpose:** Represents a purchase made by a customer, including who handled it and its current status.

- **Order items (`StavkeNarudzbe`)** – Stores individual items in an order. Junction table for the many-to-many relationship between orders and products.  
  **Attributes:** `StavkaID` (PK), `NarudzbaID` (FK), `ProizvodID` (FK), `Kolicina`, `Popust`  
  **Constraints & validation:**  
  - `Kolicina` must be a positive number  
  - `Popust` (discount) must be between 0 and 100  
  **Purpose:** Represents each product included in an order, with quantity and any discounts applied.

- **Payments (`Placanja`)** – Stores payment information for orders.  
  **Attributes:** `PlacanjeID` (PK), `NarudzbaID` (FK), `DatumPlacanja`, `Iznos`, `NacinPlacanja`  
  **Constraints & validation:**  
  - `NacinPlacanja` must be one of: 'Kartica', 'PayPal', 'Bankovni transfer', 'Gotovina'  
  **Purpose:** Records financial transactions linked to orders and ensures correct payment method usage.

---

## Delivery and addresses
- **Deliveries (`Dostave`)** – Stores delivery details for orders.  
  **Attributes:** `DostavaID` (PK), `NarudzbaID` (FK), `AdresaDostave`, `StatusDostave`, `DatumSlanja`, `DatumDostave`  
  **Constraints & validation:**  
  - `StatusDostave` must match one of the predefined delivery statuses  
  **Purpose:** Represents the shipping process for each order and tracks delivery status and dates.

- **Addresses (`Adrese`)** – Stores addresses linked to cities. Can also be linked to customers, employees, manufacturers, and deliveries.  
  **Attributes:** `AdresaID` (PK), `GradID` (FK), `Ulica`  
  **Purpose:** Standardizes location information for deliveries, users, and manufacturers.

- **Cities (`Gradovi`)** – Stores city details linked to countries.  
  **Attributes:** `GradID` (PK), `DrzavaID` (FK), `Naziv`, `PostanskiBroj`  
  **Purpose:** Represents the city in which an address is located, useful for filtering and reporting.

- **Countries (`Drzave`)** – Stores country details, including unique codes, calling codes, and continent.  
  **Attributes:** `DrzavaID` (PK), `Naziv`, `SifraDrzave`, `PozivniBroj`, `Kontinent`  
  **Purpose:** Represents countries for addresses, enabling international shipping and regional analytics.

---

## Reviews
- **Reviews (`Recenzije`)** – Stores customer reviews for products. Ratings are limited from 1 to 5.  
  **Attributes:** `RecenzijaID` (PK), `KupacID` (FK), `ProizvodID` (FK), `Ocjena`, `Komentar`, `DatumRecenzije`  
  **Constraints & validation:**  
  - `Ocjena` must be between 1 and 5  
  **Purpose:** Allows customers to give feedback on products, including rating and comments.

---

## Relationships summary
- **Customers → Orders → Order items → Products**  
- **Orders → Payments / Deliveries**  
- **Products → Manufacturers & Categories**  
- **Addresses → Customers / Employees / Manufacturers / Deliveries**  
- **Cities → Countries**  

This structure ensures a normalized schema while enabling complex queries for reporting, analytics, and operational processes.

---

## Data seed and conventions
- Approximately **200 test records** have been added to populate the database for demonstration and testing.  
- Decimal fields such as `Cijena` (Price) and `Popust` (Discount) are rounded for precision.  
- Predefined sets for columns like `StatusDostave`, `Status` (orders), `NacinPlacanja`, and ratings (`Ocjena`) are enforced in the database.  

---

## Usage
1. Clone this repository.  
2. Open **SQL Server Management Studio (SSMS)** or another SQL Server interface.  
3. Execute `db_schema.sql` to create the E-Trgovina database and all tables.  
4. Run the data seed script to populate test data.  
5. Use the database for queries, reporting (SSRS/SSDT), or further development.  

---

## Notes
- All relationships maintain referential integrity.  
- Cascading deletes and nullification ensure smooth data maintenance without orphaned records.  
- Suitable for learning, reporting, or small-scale e-commerce applications.

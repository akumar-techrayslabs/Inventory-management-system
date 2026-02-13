# QUERY DESING FOR INVENTORY MANAGEMENT SYSTEM 

## Thes  are some queries which shows how the  INVENTORY MANAGEMENT SYSTEM Operates 

### The queries written in a manner by which we can understand the overall flow of this INVENTORY MANAGEMENT SYSTEM


# Create Organization 
## We can create multiple organizations for this system which can have multiple warehouses

```sql 

INSERT INTO ORGANIZATIONS (name, code,is_active)
VALUES ('ABC PRIVATE LIMITED', 'ABC001',TRUE);

```

# Create Master prerequisite Records
## In this we are creating some Master Tables to avoid hardcoded values and to make the sytem scalable

# Create Status Records
## This is for status which we can use later on for product_purchase, product_sale etc.
```sql

INSERT INTO STATUS_MASTER 
(status_name, status_code, is_final, is_editable)
VALUES
('DRAFT', 'D001', FALSE, TRUE),
('APPROVED', 'A001', TRUE, FALSE),
('CANCELLED', 'C001', TRUE, FALSE);

```

# Create Movements Types
## In this we can define the stock movements types like IN or OUT by this we can specify whether the stock increase or decrease


```sql 

INSERT INTO MOVEMENT_TYPE_MASTER (CODE, NAME, DIRECTION, AFFECTS_INVENTORY)
VALUES
('PURCHASE', 'PURCHASE STOCK', 'IN', TRUE),
('SALE', 'SALE STOCK', 'OUT', TRUE);

```

# Create Unit 

## It defines measurement units like Piece, Kg, Liter as Inventory can have multiple products with different units

```sql 

INSERT INTO UNIT_MASTER (NAME, SYMBOL, IS_DECIMAL_ALLOWED)
VALUES ('PIECE', 'PCS', FALSE);
VALUES ('KILOGRAM','KG',TRUE)

```


# Create Category
## Category is needed to group similar products to create like Smartphone- ios, android 

```sql 

INSERT INTO CATEGORIES( organization_id,name) VALUES(1,'ELECTRONICS');

```

# Crete Admin User 

## To create Admin Users within an Organization to handle different tasks

```sql 

INSERT INTO USERS 
(ORGANIZATION_ID, FULL_NAME, EMAIL, PASSWORD_HASH, STATUS_ID)
VALUES
(1, 'ADMIN USER', 'anish@admin.com', 'hashedpassword', 2);

```

# Create Role 
## To define user job positions as different user can have different permissions

```sql

INSERT INTO ROLES (ORGANIZATION_ID, name, description)
VALUES (1, 'WAREHOUSE MANAGER',"MANAGES WAREHOUSE OPERATIONS" );

```

# Assign Role to User
## Using this we can assign permissions based on role to users 

```sql 

INSERT INTO USER_ROLES (USER_ID, ROLE_ID)
VALUES (1, 1);

```

# Create Permissions
## Create permissions to assign which user can access what 

```sql 

INSERT INTO PERMISSIONS (NAME)
VALUES
('PURCHASE_CREATE'),
('PURCHASE_APPROVE'),
('SALES_CREATE'),
('SALES_CONFIRM');

```

# Assigning Permissions according to Roles
## we can assign different permissions to users

```sql INSERT INTO ROLE_PERMISSIONS (ROLE_ID, PERMISSION_ID)
VALUES
(1, 1),
(1, 2),
(1, 3),
(1, 4);

```
# Create Warehouse 
## It defines the storage location as Inventory must be stored somewhere

```sql
INSERT INTO WAREHOUSES 
(organization_id, name, location, status_id, manager_id)
VALUES
(1, 'MAIN WAREHOUSE', 'DELHI', 2, 1 );
```

# Create Product

```sql 

INSERT INTO PRODUCTS 
(organization_id, name, sku, category_id, unit_id,reorder_level,is_active)
VALUES
(1, 'DELL LAPTOP', 'SKU001', 1, 1, 5, TRUE);

```

# Create product Variety
## It stores products variations as same product can have multiple types.

```sql

INSERT INTO PRODUCT_VARIETIES(
    product_variant_name, product_id, sku, price
)
VALUES('LAPTOP 16 GB RAM '1,'SKU001-16',65000);

```

# Add Product Image
## A single product can have multiple images using a product_image table we can store url of images which  reduces redundancy 

```sql 

INSERT INTO PRODUCT_IMAGES(product_id, image_url, is_primary)
VALUES(1,'https://res.cloudinary.com/demo/image/upload/sample.jpg',true)

```

# Initilaze Inventory 

## We are intialize inventory values to zero to track stock row in warehouses which can be updated later on using product_purchase or customer_order 

```sql 

INSERT INTO INVENTORY 
(PRODUCT_ID, WAREHOUSE_ID, QUANTITY, RESERVED_QUANTITY)
VALUES
(1, 1, 0, 0);

```

# Create Supplier
## Stores Suppliers details to purchase items for the warehouse 

```sql 

INSERT INTO SUPPLIERS 
(ORGANIZATION_ID, NAME, EMAIL, STATUS_ID)
VALUES
(1, 'DELL SUPPLIER', 'supplier@dell.com', 2);


```

# Create Purchase Order - Initial Status ~ Draft

## It is used to create Purchase document 

```sql 

INSERT INTO PURCHASE_ORDERS
(SUPPLIER_ID, WAREHOUSE_ID, STATUS_ID, TOTAL_AMOUNT, CREATED_BY)
VALUES
(1, 1, 1, 500000, 1);

```

# Add purchase Order item 
## it is used to create information about the purchase order 

```sql 
INSERT INTO PURCHASE_ORDER_ITEMS
(PURCHASE_ORDER_ID, PRODUCT_ID, QUANTITY, UNIT_PRICE, TAX_AMOUNT, TOTAL_AMOUNT)
VALUES
(1, 1, 10, 50000, 0, 500000);
```

# Approve Purchase Order 
## It is used to approve the order to make the Product_purchase official 

```sql

UPDATE PRUCHASE_ORDERS
SET STATUS_ID = 2, APPROVED_BY = 1, APPROVED_AT = NOW()
WEHRE ID = 1;

```

# Increase Inventory
## Add the purchased items to Inventory table to track stock 

```sql 

UPDATE INVENTORY SET QUANTITY = QUANTITY + 1

```

# INSERT STOCK MOVEMENT 

## To store the history of the stocks it is needed 

```sql 

INSERT INTO STOCK_MANAGEMENT
(PRODUCT_ID, WAREHOUSE_ID, MOVEMENT_TYPE_ID, QUANTITY, STOCK_AFTER, PERFORMED_BY)
VALUES
(1, 1, 1, 10, 10, 1);

```

# Create Customer 
## It stores customer info whom the warehouse selling items

```sql 

INSERT INTO CUSTOMERS
(ORGANIZATION_ID, NAME, EMAIL, STATUS_ID, COMPANY_NAME)
VALUES
(1, 'AYUSH TRADERS', 'ayush@gmail.com', 2, 'Ayush Pvt Ltd');

```

# Create Customer Order Intially Status ~ Draft
## Creates sales document
```sql 

INSERT INTO CUSTOMERS_ORDERS
(CUSTOMER_ID, WAREHOUSE_ID, STATUS_ID, TOTAL_AMOUNT, CREATED_BY)
VALUES
(1, 1, 1, 100000, 1);

```


# Add Customer Order items

## it is used to create information about the Customer Orders

```sql 

INSERT INTO CUSTOMERS_ORDER_ITEMS
(customers_order_id, PRODUCT_ID, QUANTITY, UNIT_PRICE, TOTAL_AMOUNT)
VALUES
(1, 1, 2, 50000, 100000);

```

# Approve/Confirmn Order 

```sql 

UPDATE CUSTOMERS_ORDERS 
SET STATUS_ID = 2
WHERE ID = 1;

```

# Reduce Inventory 
## As warehouse has sold some items accordingly Inventory should get reduced

```sql 

UPDATE INVENTORY 
SET QUANTITY = QUANTITY - 2
WHERE PRODUCT_ID = 1 AND WAREHOUSE_ID = 1;

```


# Insert Stock Movement (OUT)
## Accordingly it should generate a history in the Stock Movement 

```sql 

INSERT INTO STOCK_MANAGEMENT
(PRODUCT_ID, WAREHOUSE_ID, MOVEMENT_TYPE_ID, QUANTITY, STOCK_AFTER, PERFORMED_BY)
VALUES
(1, 1, 2, 2, 8, 1);

```

# Create Payment Method
## It defines how payemnt is made (UPI, Cash, Bank)

```sql 

INSERT INTO PAYMENT_METHOD_MASTER (NAME, IS_ONLINE)
VALUES ('CASH', FALSE);
VALUES ('UPI', TRUE);

```

# Create Payment Direction
## Using this we can know whether the money is coming IN or going OUT to handle the finances later 


```sql 

INSERT INTO PAYMENT_DIRECTION_MASTER (NAME)
VALUES ('IN'), ('OUT');

```

# Payment Record 
## Store the payment information 
```sql

INSERT INTO PAYMENTS
(REFERENCE_ID, REFERENCE_TABLE, PAYMENT_DIRECTION_ID, PAYMENT_METHOD_ID, AMOUNT, STATUS_ID, PAID_BY, PAYMENT_DATE)
VALUES
(1, 'CUSTOMERS_ORDERS', 1, 1, 100000, 2, 1, CURDATE());

```

# Fetching All Products with Category
## For this we are using join to according to the categories

```sql 

SELECT p.name AS PRODUCT_NAME, c.name AS CATEGORY_NAME
from PRODUCTS p
INNER JOIN CATEGORIES c on p.category_id = c.id  

```

# Get Inventory Reports 

```sql

SELECT p.NAME AS PRODUCT_NAME , w.NAME AS WAREHOUSE_NAME , i.QUANTITY AS QUANTITY
FROM INVENTORY i 
INNER JOIN PRODUCTS p ON i.PRODUCT_ID = p.ID 
INNER JOIN WAREHOUSES w ON i.WAREHOUSE_ID = w.ID;

```

# Get Purchase Orders with Supplier
```sql 

SELECT po.id, s.name AS SUPPLIER NAME, po.total_amount AS TOTAL AMOUNT
FROM PURCHASE_ORDERS po
INNER JOIN SUPPLIERS s ON po.supplier_id = s.i

```

# Get Customer Orders with Customer
```sql

SELECT co.id, c.name AS CUSTOMER_NAME, co.total_amount AS TOTAL_AMOUNT
FROM CUSTOMERS_ORDERS co
INNER JOIN CUSTOMERS c ON co.customer_id = c.id;

```
# Stock History

```sql
Get Stock History
SELECT p.name, sm.quantity as STOCK_NAME, sm.stock_after 
FROM STOCK_MANAGEMENT sm
JOIN PRODUCTS p ON sm.product_id = p


```

# Update Product Price
```sql
UPDATE PRODUCT_VARIETIES
SET price = 70000
WHERE id = 1;

```

# Soft Delete Customer
```sql 

UPDATE CUSTOMERS
SET deleted_at = NOW(),
    is_active = 0
WHERE id = 1;


```


# Total Sales
```sql 

SELECT SUM(total_amount) AS total_sales
FROM CUSTOMERS_ORDERS
WHERE status_id = 3;

```
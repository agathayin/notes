### version

```
SELECT version();
```

### time

```
SELECT now();
```

### create database

```
CREATE DATABASE colors;
```

### see all databases

```
\l
```

### connect database

```
\c colors
```

### add table

```
CREATE TABLE colors (ColorID int, ColorName char(20));
```

### add data

```
INSERT INTO colors VALUES(1, 'red'), (2, 'blue'), (3,'green');
```

### query

```
SElECT * FROM colors;

SELECT name, cost
FROM manufacturing.products
WHERE category_id = 3;

SELECT name, cost
FROM manufacturing.products
WHERE cost < 3;
```

### join

```
SELECT product.product_id,
    products.name AS product_name,
    categories.name AS category_name,
    categories.market
FROM manufacturing.products JOIN manufacturing.categories
ON products.category_id = categories.category_id
WHERE market = 'industrial';
```

### view

```
CREATE VIEW manufacturing.product_details AS
SELECT product.product_id,
    products.name AS product_name,
    categories.name AS category_name,
    categories.market
FROM manufacturing.products JOIN manufacturing.categories
ON products.category_id = categories.category_id
WHERE market = 'industrial';
```

query: return employees in the south building

```
SELECT employees.first_name,
    employees.last_name,
    departments.building
FROM human_resources.employees JOIN human_resources.categories
ON employees.deparment_id = deparments.deparment_id
WHERE departments.building = 'South';
```

### indexes

```
CREATE INDEX products_name_idx
    ON manufacturing.products USING btree
    (name ASC NULLS LAST)
```

### enum

contraints - Create Check - definition

```
market = 'domestice' OR martket = 'industrial'
```

### stop

```
sudo -u postgres /Library/PostgreSQL/17/bin/pg_ctl -D /Library/PostgreSQL/17/data stop
```

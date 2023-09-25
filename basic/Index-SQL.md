## Optimize Database with Indexes

- Indexes speed up SQL selections
- Indexes cannot span across tables
- Indexes impose a slight **penalty** on SQL **INSERT, UPDATE, DELETE** statements

#### 1. Create and Drop an index

Syntax: 
```CREATE INDEX [index-name] ON [table]([field set]);```
```DROP INDEX [index-name] ON [table];```

E.g: 

```
CREATE INDEX idx_title ON books(title);
CREATE INDEX idx_first_last_name ON students(first_name, last_name);
 ```

```DROP INDEX idx_title ON books;```

#### 2. Show indexes

Show all indexes for a table

Syntax: ```SHOW INDEX FROM [table-name]```

E.g: 

```SHOW INDEX FROM members;```


#### 3. Analyze a SQL command 

Syntax: ```EXPLAIN ANALYZE [SQL command];```

#### 4. How to get around the absence of cross table indexes in MySQL?

Create individual indexes for the tables involved and join the tables in an SQL while specifying the indexes to use for each table.

```
SELECT set_name, hobbyist_name, country
FROM playset
USE INDEX(idx_set_name)
JOIN
hobbyist
USE INDEX(idx_hobbyist_name)
ON playset.item_number = hobbyist.item_number
WHERE set_name = "Fire Truck" AND hobbyist_name = "Harrison Kong";
 ```

#### 5. Which of the following SQL operations will benefit ?

SELECT operations where **few rows in the table meet the criteria**.

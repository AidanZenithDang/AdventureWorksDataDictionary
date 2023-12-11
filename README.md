# AdventureWorksDataDictionary
A data dictionary for Adventure Works

The descriptions data is extracted by running the following queries in the database to get attribute and object level descriptions. 

```sql 
CREATE VIEW vw_table_info AS
SELECT
  it.TABLE_SCHEMA,
  it.TABLE_NAME,
  CONCAT(it.TABLE_SCHEMA, '.', it.TABLE_NAME) AS FULL_NAME,
  OBJECT_ID(CONCAT(it.TABLE_SCHEMA, '.', it.TABLE_NAME)) AS table_id
FROM
  INFORMATION_SCHEMA.TABLES AS it
SELECT
  t.full_name AS table_name,
  CAST(ep.value AS nvarchar(MAX)) AS description
FROM
  vw_table_info AS t
  INNER JOIN sys.extended_properties AS ep ON ep.major_id = OBJECT_ID(t.full_name)
  AND ep.minor_id = 0
ORDER BY
  t.full_name;

SELECT
  t.TABLE_NAME,
  t.TABLE_SCHEMA,
  t.full_name AS table_name,
  c.COLUMN_NAME AS attribute_name,
  CAST(ep.value AS nvarchar(MAX)) AS description
FROM
  vw_table_info AS t
  INNER JOIN INFORMATION_SCHEMA.COLUMNS AS c ON t.full_name = CONCAT(c.TABLE_SCHEMA, '.', c.TABLE_NAME)
  INNER JOIN sys.extended_properties AS ep ON ep.major_id = t.table_id
  AND ep.minor_id = c.ORDINAL_POSITION
ORDER BY
  t.full_name,
  c.ORDINAL_POSITION
```

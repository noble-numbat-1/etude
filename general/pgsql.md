# PGSQL

## STATISTIC QUERY

Count row in Table

```sql
WITH tbl AS (
  SELECT table_schema, TABLE_NAME
  FROM information_schema.tables
  WHERE TABLE_NAME not like 'pg_%'
  AND table_schema in ('public')
)
SELECT
  table_schema, TABLE_NAME,
  (xpath('/row/c/text()', query_to_xml(format('select count(*) as c from %I.%I', table_schema, TABLE_NAME), FALSE, TRUE, '')))[1]::text::int AS rows_n
FROM tbl
ORDER BY rows_n DESC;
```

## BACKUP AND RESTORE

### `pg_dump`

It extracts a particular Postgres database into a script file.

```sql
pg_dump -U username -d database_name -f backup_file.sql
```

## KEY TERM

- CTE: Common Table Expressions
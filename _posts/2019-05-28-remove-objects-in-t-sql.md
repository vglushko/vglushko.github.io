---
layout: post
title: "Remove Tables, Constraints, Functions from a Database Schema"
date: 2019-05-28 12:49:46 +0300
category: snippets
tags: 
  - mssql
  - rdbms
---

SQL snippets on how to empty a Microsoft SQL Server database by removing all tables, constraints and functions.
<!--more-->

# Remove all constraints:

```sql
DECLARE @sqlConstraints NVARCHAR(MAX) = N'';
SELECT @sqlConstraints = @sqlConstraints 
    + N'ALTER TABLE ' + quotename(s.name) + N'.' + quotename(t.name) 
        + N' DROP CONSTRAINT ' + quotename(o.name) + ';' + CHAR(13)
FROM sys.objects o 
JOIN sys.tables t ON o.parent_object_id = t.[object_id]
JOIN sys.schemas s ON t.[schema_id] = s.[schema_id]
WHERE o.[type] IN ('D','C','F','UQ') AND s.[name] = 'dbo'

exec sp_executesql @sqlConstraints;
```

# Remove all tables:
```sql
DECLARE @sqlTables NVARCHAR(MAX) = N'';
SELECT @sqlTables = @sqlTables 
    + N'DROP TABLE ' + quotename(s.[name]) + N'.' + quotename(t.[name]) + N';' + CHAR(13)
FROM sys.tables t JOIN sys.schemas s ON t.[schema_id] = s.[schema_id] 
WHERE t.[type] = 'U' AND s.[name] = 'dbo'

exec sp_executesql @sqlTables;
```

# Remove all functions:
```sql
DECLARE @sqlFunc NVARCHAR(MAX) = N'';
SELECT @sqlFunc = @sqlFunc + N'DROP FUNCTION ' + quotename(schema_name(schema_id)) + N'.' + quotename(name) + N';' + CHAR(13)
FROM sys.objects
WHERE [type] IN ('TF', 'IF', 'FN')

exec sp_executesql @sqlFunc
```

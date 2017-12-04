---
title: T-SQL magic
date: 2016-11-04 17:39:23
tags:
---

## How to get indexes of current database?

```sql
select * from sys.indexes
```

## How to get replication articles?

```sql
SELECT 
  msp.publication AS PublicationName,
  msa.publisher_db AS DatabaseName,
  msa.article AS ArticleName,
  msa.source_owner AS SchemaName,
  msa.source_object AS TableName
FROM distribution.dbo.MSarticles msa
JOIN distribution.dbo.MSpublications msp ON msa.publication_id = msp.publication_id
ORDER BY 
  msp.publication, 
  msa.article
```

```sql
select * from syspublications
select * from sysarticles where pubid = 3
```

- [More info mation get get replication related table.](https://msdn.microsoft.com/en-us/library/ms179855.aspx)
- [Replication infomation](https://msdn.microsoft.com/en-us/library/dn198334.aspx)
- [Replication stored procedure](https://msdn.microsoft.com/en-us/library/ms174364.aspx)

## Useful replication stored procedure.
```sql
exec sp_dropsubscription
    @publication='**-Publication',
    @article= N'all',
    @subscriber = 'WIN-VPE**FSH8IJ'

exec sp_droppublication
    @publication='**-Publication'

exec sp_removedbreplication @dbname='**-local' 
```

## How to close connect and drop db in sql server?

```sql
alter database [dbname] set SINGLE_USER with rollback immediate
drop database [dbname]
```

## Other collections

- [SqlServer important queries](http://www.c-sharpcorner.com/article/50-important-queries-in-sql-server/)
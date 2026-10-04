# Automated SQL Server Index Maintenance

## Objective

The Automated SQL Server Index Maintenance project was developed to automate the identification and rebuilding of highly fragmented indexes across database tables.

The goal was to improve database maintenance efficiency and support query performance by automatically identifying indexes that meet predefined fragmentation and page-count thresholds, rebuilding the affected indexes, and recording the execution status for monitoring and auditing.

### Skills Learned

* SQL Server database administration and maintenance
* T-SQL stored procedure development
* Index fragmentation analysis and optimization
* Query performance tuning
* Dynamic SQL development
* SQL Server Dynamic Management Views (DMVs)
* Database metadata and object management
* Database automation
* Execution logging and monitoring
* Backup, recovery, and database maintenance concepts

### Tools Used

* Microsoft SQL Server
* SQL Server Management Studio (SSMS)
* T-SQL
* SQL Server DMVs
* INFORMATION_SCHEMA
* Dynamic SQL

## Steps

Below are the key steps implemented in the automated index maintenance process:

### 1. Database Selection

The stored procedure accepts the database name as an input parameter, allowing the same procedure to be used for different databases.

```sql
EXEC database_index_rebuild 'TechHealthDb';
```

This avoids hard-coding a single database and makes the solution reusable.

*Ref 1: Stored Procedure Execution*

This screenshot shows the execution of the stored procedure with the target database name.

![Stored Procedure Execution](https://github.com/Vishnupd/Automated-SQL-Server-Index-Maintenance/blob/main/Stored%20procedure.png)
![Stored Procedure Execution](https://github.com/Vishnupd/Automated-SQL-Server-Index-Maintenance/blob/main/Stored%20procedure_2.png)

### 2. Table Discovery

The procedure retrieves base tables from the selected database using `INFORMATION_SCHEMA.TABLES`.

Tables containing log-related names are excluded from the maintenance process to avoid unnecessary index operations on logging tables.

### 3. Index Fragmentation Analysis

The procedure uses `sys.dm_db_index_physical_stats` to analyze index health and retrieve information such as:

* Average fragmentation percentage
* Page count
* Index type
* Object ID
* Index ID

The maintenance criteria are:

```sql
WHERE avg_fragmentation_in_percent > 65
  AND page_count > 1000
  AND f.index_type_desc != 'HEAP'
```

Only indexes meeting these conditions are selected for maintenance.

### 4. Automated Index Rebuild

When a table contains indexes meeting the defined criteria, the procedure dynamically generates an `ALTER INDEX` statement.

```sql
ALTER INDEX ALL
ON DatabaseName.dbo.TableName
REBUILD WITH (FILLFACTOR = 80);
```

The procedure uses `ALTER INDEX ALL` to rebuild the indexes associated with the selected table.

A FILLFACTOR of 80% is applied to leave free space on index pages, which can help reduce page splits in workloads with frequent data modifications.

*Ref 4: Index Rebuild Execution*

This screenshot shows the index rebuild operation being executed successfully.

![Index Rebuild](https://github.com/Vishnupd/Automated-SQL-Server-Index-Maintenance/blob/main/Table_Logging.png)

### 5. Execution Logging

A dedicated logging table named `log_db_index_rebuild` records the outcome of each maintenance operation.

The table contains:

| Field          | Description                              |
| -------------- | ---------------------------------------- |
| execution_date | Date and time of execution               |
| db_name        | Database where maintenance was performed |
| table_name     | Table whose indexes were rebuilt         |
| status         | Execution result                         |

Example:

| execution_date   | db_name      | table_name | status  |
| ---------------- | ------------ | ---------- | ------- |
| 2026-10-03 21:30 | TechHealthDb | Customers  | Success |
| 2026-10-03 21:32 | TechHealthDb | Orders     | Success |

This provides an audit trail for tracking database maintenance activities.

*Ref 5: Maintenance Log*

This screenshot shows the `log_db_index_rebuild` table containing the execution results.

![Index Rebuild Log](link-to-image)

## Results / Benefits

* Automated a repetitive SQL Server database maintenance task.
* Reduced the need for manual index fragmentation analysis.
* Applied consistent fragmentation and page-count thresholds.
* Avoided rebuilding small or low-fragmentation indexes.
* Improved maintainability through reusable T-SQL automation.
* Created an execution log for monitoring and auditing.
* Supported database performance and index maintenance activities.
* Demonstrated practical SQL Server DBA and T-SQL development skills.

## Skills Demonstrated

**SQL Server | T-SQL | Dynamic SQL | Stored Procedures | Index Maintenance | Index Fragmentation | Query Performance | DMVs | Database Administration | Database Automation | SQL Server Metadata | Logging & Monitoring | Database Optimization**

## My Contribution

Designed and implemented a T-SQL stored procedure to automate SQL Server index maintenance. I developed the logic to dynamically identify application tables, retrieve index fragmentation statistics, apply fragmentation and page-count thresholds, rebuild qualifying indexes, and record execution results for operational tracking.


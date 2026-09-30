# Snowflake

Snowflake is a cloud-based column-based storage data warehouse that runs on AWS, Azure, and Google Cloud. It's a fully managed service that separates compute and storage, so you only pay for what you use. It automatically scales to handle large datasets and delivers fast SQL query performance. It's popular for analytics, business intelligence, and data pipelines, and features unique data-sharing capabilities that let you securely share data across organizations without copying. Excellent for data analytics.

## Object hierarchy

<p align="center">
  <img width="630" height="333" src=".attachments/object_hierarchy.png">
</p>

Session variables let you store and reuse values within a script. Set one with the SET function, for example, SET min_users = 100 then reference it anywhere in that session using dollar sign min_users.

Account parameters sit at the top and can't be overridden. Whatever is set at the account level is what applies, things like PERIODIC_DATA_REKEYING.

Session parameters work differently. The account sets a default, an administrator can override it for a specific user, and the user can override it again within their active session. The lowest level at which the parameter is set wins.

Virtual warehouse parameters follow a simpler two-level chain: account default, then overridden at the warehouse itself. STATEMENT_TIMEOUT_IN_SECONDS is the classic one — you might want a tighter timeout on your dashboard warehouse than the account default allows.

Finally, database, schema, and table parameters cascade down through the object hierarchy. Set a default at the account level, override it at the database, narrow it further at the schema, and override it again on a specific table. DATA_RETENTION_TIME_IN_DAYS works this way, your account might default to one day, but your more important billing tables get thirty. The rule across all four types is the same: the lowest level at which the parameter is set will be used, and that value wins.

<p align="center">
  <img width="729" height="344" src=".attachments/hierarchy_parameters.png">
</p>

## Micro partitioning and pruning

When data is loaded into Snowflake, it automatically gets divided into micro-partitions, which are chunks of on-disk storage on the order of 50 to 500 MB of uncompressed data. For every micro-partition that Snowflake creates it stores metadata including min and max value for each column, the row count, null counts and distinct value counts. This metadata is stored in the cloud services layer and is maintained automatically as data is loaded and updated.

Pruning only works well when the column you are trying to filter on is well-clustered, meaning that the rows with similar values are grouped together in the same micro-partitions.

<p align="center">
  <img width="750" height="260" src=".attachments/micro_partitions.png">
</p>

Clustering is like an index in traditional databases like postgres.

A clustering key makes sense when three things are true: the table is large enough that pruning actually matters, 100Gb or bigger tables, you're consistently filtering on the same column; and that column is poorly clustered, meaning pruning isn't working for it.

<p align="center">
  <img width="700" height="350" src=".attachments/clustering.png">
</p>

## Table Types and Views

<p align="center">
  <img width="620" height="400" src=".attachments/table_types1.png">
</p>

<p align="center">
  <img width="650" height="200" src=".attachments/table_types2.png">
</p>

<p align="center">
  <img width="800" height="400" src=".attachments/table_types3.png">
</p>

**Time Travel** is like a version control within snowflake. Time Travel lets you query data as it existed at any point within the retention window. In standard edition you only have 1 day of data retention, while in enterprise you have up to 90 days.

**Fail-safe** is an additional layer of protection. Beyond the Time Travel window, Snowflake automatically holds your data for a further seven days in Fail-safe. You can't query it yourself during that period, but if you need it, Snowflake Support can recover it for you. This is your last resort. 

## Security and Access Control
RBAC: Role Based Access Control, Privileges, such as SELECT, INSERT, etc are granted to roles (analyst_role), which are assigned to users (some_email). Never assign privileges to users, don't skip the role step.

ACCOUNTADMIN for account-level administration, SECURITYADMIN for the security landscape, and SYSADMIN for data infrastructure.

Three grants are required to give the a role access to a table: USAGE on the database, USAGE on the schema, and SELECT on the table. The most common mistake is granting SELECT and forgetting the USAGE grants.The hierarchy enforces that every level of access is deliberate.

There is an additional level of security available, column-based security, this approach uses a masking policy to show the column with the values are masked. 

## Management & Governance
Credits are the unit of cost. One credit equals to one hour of a Standard Gen 1 X-Small warehouse running continuously. Credit consumption depends on warehouse type and size. Credit consumption doubles when the warehouse size increases; XS consumes 1, S consumes 2 credits, M consumes 4 credits, L consumes 8 credits and so on. For example, A Large (L) warehouse consumes 8 credits per hour. if it runs for 3.5 hours it will consume 28 (8 × 3.5) credits in total, assuming it auto suspends when it finishes.
Virtual warehouses usage is billed per second with a one-minute minimum. Cloud services layer (authentication, query optimization, metadata) are only charged if the cost is above 10% of daily warehouse compute.

ACCOUNT_USAGE is a schema inside the SNOWFLAKE system database that Snowflake maintains for every account. It contains views that expose historical data: queries run, credits consumed, storage used, logins recorded etc. The data has a latency of up to three hours, so it's not suitable for real-time monitoring and most views retain data for 365 days, giving you a full year of history to query against.

| Feature          | ACCOUNT_USAGE                                | INFORMATION_SCHEMA                    |
|------------------|----------------------------------------------|---------------------------------------|
| Scope            | History of entire account, all databases     | Current state of your database        |
| Data latency     | Up to 3 hours                                | Low latency                           |
| Retention        | Up to 365 days                               | Up to 6 months (varies by view)       |
| Dropped objects  | Included                                     | Not included                          |
| Primary use case | Historical audit and cost analysis           | Current state and metadata            |

WAREHOUSE_METERING_HISTORY answers which warehouses consumed the most credits.

ACCOUNT_USAGE.QUERY_HISTORY records every query run in the account - who ran it, on which warehouse, how long it took, and how much data it scanned.

STORAGE_USAGE tracks how much storage the account is consuming over time - both active data storage and Fail-safe storage. The raw values are in bytes; dividing by 1024 to the power of three converts to gigabytes.

ACCOUNT_USAGE.LOGIN_HISTORY records every login attempt against the account - successful or not.

Zero-copy cloning does not write new micro-partitions at creation time. The clone and the source share the same physical storage. Storage cost only increases as the two tables diverge.

## Stages

There are two types of stages: internal and external. Internal stages are managed by Snowflake. External stages point to a location on a external provider such as Amazon S3, Azure Blob Storage, or Google Cloud Storage, and let you load data from there into Snowflake tables.

There are three types of internal stages: user, table and named. A user stage is a personal staging area for a single user. A table stage is tied to a specific table therefore multiple users can stage files there, but they can only load into that one table. These two types of stages are created automatically by Snowflake. A named stage is a database object created in a schema, making it shareable across roles and sessions. Similarly, an external stage can have a named stage that's location is on a cloud provider and acts as a pointer to it's external cloud location.

COPY INTO is the command that moves data from a stage into a Snowflake table. The batch solution. The target is the fully qualified table name. FROM specifies the stage and path. FILE_FORMAT references the named format object. And ON_ERROR controls what happens if Snowflake encounters a bad row. This single command replaces what would otherwise be a multi-step ETL process. The data is already in the stage. COPY INTO reads it, parses it, and loads it.
Before loading, run the same COPY INTO command with VALIDATION_MODE = RETURN_ERRORS. Snowflake parses the file and surfaces any rows that would fail, without touching the target table.
Errors are written to the INFORMATION_SCHEMA.COPY_HISTORY table.

A Snowpipe wraps a COPY INTO statement and triggers it automatically as new files arrive in a stage. Snowpipe fires each time a new file lands. Loads happen in micro-batches within minutes. And because it's serverless, there's no warehouse to provision or manage. This is the streaming solution.

## Functions

GET_PRESIGNED_URL() generates a time-limited download link that requires no Snowflake credentials. This link can be shared with anyone so they can download the data: 
`SELECT GET_PRESIGNED_URL(@my_stage, 'file_name.json', 3600)`

## Snowflake Query Execution Order

| Order | Clause | Purpose |
|-------|--------|---------|
| 1 | FROM | Identifies the source table(s) to query from |
| 2 | WHERE | Filters rows based on specified conditions before grouping |
| 3 | GROUP BY | Groups rows into subsets based on specified columns |
| 4 | HAVING | Filters groups based on aggregate function conditions |
| 5 | SELECT | Selects which columns and expressions to return |
| 6 | Window Functions | Computes values over a set of rows (ROW_NUMBER, RANK, LAG, LEAD, etc.) |
| 7 | QUALIFY | Filters rows based on window function results |
| 8 | DISTINCT | Removes duplicate rows from results |
| 9 | ORDER BY | Sorts the result set in ascending or descending order |
| 10 | LIMIT | Restricts the number of rows returned |

- **Window Functions** are calculated after SELECT but before ORDER BY
- **QUALIFY** is used to filter results based on window function conditions.

## Query Best Practices

### Column Selection
- **Avoid SELECT *** – Always specify only the columns you need to reduce data transfer and improve performance
- **Select relevant columns** – Unnecessary columns increase I/O and processing time
- **Use column aliases** – Make queries more readable and maintainable

### WHERE Clauses & Filtering
- **Add WHERE clauses early** – Filter data as early as possible to reduce the dataset size
- **Avoid functions in WHERE clauses** – Functions prevent partition pruning and index usage (e.g., `WHERE YEAR(date_col) = 2024` is slower than `WHERE date_col >= '2024-01-01'`)
- **Use BETWEEN instead of OR** – `BETWEEN` is more efficient than multiple OR conditions
- **Filter on clustered/partitioned columns** – Takes advantage of Snowflake's clustering and micro-partitioning
- **Avoid NOT IN with NULL values** – Can return unexpected results; use NOT EXISTS instead

### Query Optimization
- **Use LIMIT clauses** – Restrict result sets when exploring data or for pagination
- **Use CTEs (Common Table Expressions)** – Improves readability and allows query optimization
- **Avoid correlated subqueries** – They execute repeatedly; use JOINs or window functions instead
- **Use EXPLAIN PLAN** – Analyze query execution before running large queries
- **Avoid multiple passes** – Consolidate logic to avoid scanning tables multiple times

### Joins & Relationships
- **Use appropriate JOIN types** – Only use INNER, LEFT, RIGHT, or FULL as needed
- **Join on indexed/clustered columns** – Improves join performance
- **Avoid self-joins when possible** – Use window functions as an alternative
- **Filter before joining** – Reduce dataset size before performing joins

### Aggregations
- **Use GROUP BY efficiently** – Group by necessary columns only
- **Use HAVING to filter aggregates** – Not WHERE; HAVING applies after grouping
- **Avoid GROUP BY on non-aggregated columns** – Increases processing unnecessarily
- **Use QUALIFY for window functions** – Instead of subqueries to filter window function results

### Data Types & Casting
- **Use appropriate data types** – Don't store numbers as strings or vice versa
- **Avoid unnecessary casting** – Casting can prevent partition pruning
- **Use CAST or :: for conversions** – Only when necessary; keep data types consistent

### Performance & Cost
- **Monitor query cost** – Use QUERY_HISTORY to track and optimize expensive queries
- **Use approximate functions** – `APPROX_PERCENTILE()`, `APPROX_COUNT_DISTINCT()` for large datasets when precision isn't critical
- **Leverage caching** – Identical queries within 24 hours use cached results
- **Materialize views** – Create materialized views for frequently accessed aggregations
- **Avoid SELECT in subqueries unnecessarily** – Push aggregations down to reduce rows

### Code Quality
- **Use meaningful aliases** – Make queries self-documenting
- **Format queries consistently** – Use proper indentation and line breaks
- **Comment complex logic** – Explain the "why" behind complex calculations
- **Test on smaller datasets first** – Avoid running expensive queries on entire tables

### Snowflake-Specific
- **Use QUALIFY clause** – Simplifies filtering on window functions (Snowflake feature)
- **Leverage zero-copy cloning** – For testing without data duplication
- **Use dynamic SQL sparingly** – Stored procedures with dynamic SQL can be harder to optimize
- **Consider semi-structured data** – Use VARIANT type efficiently for JSON/nested data

### Anti-Patterns to Avoid
- ❌ SELECT * without specific column needs
- ❌ Functions in WHERE clauses
- ❌ NOT IN with subqueries returning NULL
- ❌ Correlated subqueries
- ❌ Excessive OR conditions
- ❌ Querying without LIMIT during exploration
- ❌ Joining before filtering
- ❌ Ignoring query execution plans

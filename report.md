# Technical Report: Data Engineering Training Program

## Dontae

## Key areas to learn
- Part 1: Career path and opportunities
- Part 4: PowerBI
- Part 6/7: Projects

## Fallback materials
- This technical writeup was taken from 04 main sources: 
    + Fundamentals of data engineering, O'reily
    + Kimball (https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/)
    + Materials in CSC261 database management
    + Completed DE bootcamp by Zach Wilson
    + Materials in DSCC202: ETL with Databricks (https://github.com/donkhoanguyen/dscc202-402-spring2025)
- During work if I am to miss out on key practices, those will be materials to which I will refer

## Table of Contents
1. [Data Engineering Overview (Week 1)](#1-data-engineering-overview-week-1)
2. [Relational Databases (SQL - SQL Server) (Weeks 2-3)](#2-relational-databases-weeks-2-3)
3. [ETL - Data Warehouse (Weeks 4-5)](#3-etl---data-warehouse-weeks-4-5)
4. [Data Visualization - Business Intelligence with Power BI (Week 6)](#4-data-visualization---business-intelligence-week-6)
5. [ETL Project (Weeks 7-8)](#5-etl-project-weeks-7-8)
6. [DataLake - Big Data (Weeks 9-10)](#6-datalake---big-data-weeks-9-10)
7. [Big Data Project (Weeks 11-12)](#7-big-data-project-weeks-11-12)

## 1. Data Engineering Overview (Week 1)
### 1.1 Tổng quan về Data Engineer
- Definition and role of Data Engineering
- Key responsibilities and skills
- Career path and opportunities

### 1.2 Các công nghệ trong Data Engineer
- Overview of essential technologies
- Technology stack components
- Industry standards and best practices

## 2. Relational Databases (Weeks 2-3)
### 2.1 Tổng quan về CSDL, Truy vấn dữ liệu
- Introduction to RDBMS
- SQL Server architecture
- Basic database concepts

### 2.2 Queries and Practice (Truy vấn SQL)
- SELECT statements
- JOIN operations
- Aggregation functions
- Subqueries and CTEs

```
WITH cte1 as (
    Select 
        a.att1, a.att2, ...
    from some_table as a
    join some_other_table as a on a.att1 = b.att1
    where a.att1 = f'{something}' and a.att2 > some_number
)

select cte1.att1, avg(cte2.att2)
from cte1
group by cte1.att1 order by some_other_column 

// I can move 'with cte1' into the cte1 call in the main select query and it will become a subqueries
// but if some submission got subqueries it's most likely note my code
```


### 2.3 Database Design (Thiết kế Database)
Upon entry I was interviewed star vs snowflake design, ER modelling (dim/fact) and 5 basic types of normalization. On best practices
- Normalization
    + Know my functional dependencies
    + I normalize all the way to 3NF before denormalizing as optimization (I refactor a lot)
    + Use surrogate keys (auto-created ID) to allow weak entities (no primary key)
- Entity-Relationship modeling
    + Know my cardinalities (1:1, 1:N, M:N).
    + Use meaningful and consistent attributes and tables' names. I will mainly use name1_name2 to be pythonic
    + Capture all constraints such as mandatory/optional participation and role names in complex relations.
    + Use weak entities when entities cannot exist without another entity.
- Database schema design
    + Define constraints in my DDL 
    + Use correct dtypes, with scaling in mind


### 2.4 Procedure, Function, Điều khiển logic
- Stored Procedures
```
CREATE PROCEDURE update_user_email
    @user_id INT,
    @new_email VARCHAR(100)
AS
BEGIN
    \\ logic here
    UPDATE Users
    SET Email = @new_email
    WHERE ID = @user_id;
END;

EXEC update_user_email @user_id = 1, @new_email = 'new@example.com';
```

- Functions
```
\\ Ima start using this more
CREATE FUNCTION get_user_age (@birth_date DATE)
RETURNS INT
AS
BEGIN
    DECLARE @age INT;
    SET @age = DATEDIFF(YEAR, @birth_date, GETDATE());
    RETURN @age;
END;

SELECT Name, dbo.get_user_age(birth_date) AS age FROM Users;
```

- Control flow statements
```
If ... begin ... END
else ... begin ... END;

While ... begin ... END

CASE ... WHEN ... ELSE ... END
```
- Error handling
```
BEGIN TRY ...
END TRY

BEGIN CATCH ...
END CATCH
```

### 2.5 View, Index, Transaction, Lock, Deadlock, Isolated level
- Views and Indexes
```
CREATE VIEW view1 AS
Here is just any select query;

CREATE INDEX idx_users_email ON Users(Email);
```
- Transactions and ACID properties
- Locking mechanisms
    + Locks control concurrent access to data to avoid conflicts.
    + Types of locks: Shared (read), Exclusive (write).
- Deadlock prevention
    + Deadlock: when two or more transactions wait indefinitely for each others locks.
    + Example: 
    ```
    \\ Query 1: 
    update ... where id = val_1
    update ... where id = val_2

    \\ Query 2:
    update ... where id = val_2
    update ... where id = val_1

    \\ 2 queries wait indefinitely for each other
    ```
    + How to fix: 
        + Access resources in the same order (id = val_1 -> id = val_2)
        + Keep transactions short
- Isolation levels
Isolation levels control how and when the changes made by one transaction become visible to others:

Level	            || Description	                        || Possible Issues
READ UNCOMMITTED	|| Allows dirty reads (lowest level)	|| Reading uncommitted data
READ COMMITTED	    || Prevents dirty reads	                || Non-repeatable reads
REPEATABLE READ	    || Prevents non-repeatable reads	    || Phantom reads possible
SERIALIZABLE	    || Highest isolation, no phantom reads	|| Can reduce concurrency


### 2.6 Tuning SQL
- Query optimization
    + Order of execution: 
        from -> on -> join -> where -> groupby -> having -> select -> orderby -> limit
    + Read less at every stage (eg: filter early with where, do not select *)
- Index optimization
    + Create index for columns that will be in a lot of where / orderby
- Performance monitoring

## 3. ETL - Data Warehouse (Weeks 4-5)
### 3.1 Tổng quan về datawarehouse và ETL
- Data warehouse concepts
- ETL process overview
- Data warehouse architecture (of ELT)

    [ Data Sources ]
        |
        v         Extract - Load
    [ Data Lake (Raw) ]
        |
        v         ETL
    [ Data Warehouse (Integrated, Cleaned) ]
        |
        +---> [ Sales Data Mart ]
        |
        +---> [ Finance Data Mart ]
        |
        +---> [ HR Data Mart ]


### 3.2 Các bước ETL - Extract, transform load
- Extract phase
- Transform phase
- Load phase
- Data quality and validation
    - Quality and validation should be ran thru tests
    - My implementation: 

### 3.3 SQL Server Integration Services (SSIS)
- SSIS architecture
    SQL Server ETL tools
- Control Flow
- Data Flow
- Variables and expressions
- Error handling
- Package deployment

### 3.4 Xây dựng CSDL đa chều trong DW
- Star schema
- Snowflake schema
- Fact and dimension tables - this part is basically ERD
- Slowly changing dimensions
    Type1: overwrite old data
    Type2: create new record (row)
    Type3: create new dimension (columns - for dimensions that get updated regularly in batch)

## 4. Data Visualization - Business Intelligence (Week 6)
### 4.1 Tổng quan về phân tích và trực quan hóa dữ liệu
- Principles of data visualization
- Business Intelligence concepts
- Data storytelling

### 4.2 Thực hành Data Visualization - Business Intelligence trên Power BI
- Power BI architecture
- Data modeling
- DAX language
- Visualization types
- Report publishing and sharing

## 5. ETL Project (Weeks 7-8)
### 5.1 Project Overview
- Project requirements
    + Biz objectives:
    + Data sources
    + Biz requirements (model, dashboard, etc?)
- Architecture design
    [Data Sources] -> [Staging DB] -> [ELT Process (SSIS)] -> [Data Warehouse] -> [BI Tool / Data Mart]
                                                                <-> (ELT/ETL within DW)
- Technology stack
    Any

### 5.2 Implementation
- ETL pipeline development
- Data warehouse implementation
    + Create schema
    + Index/partition, especially with HDFS/ Delta FS
    + Implement SCD as necessary
- Testing and validation
    + Unit (specific ETL step) tests
    + Data validation test (if data looks right)
    + Performance test (runtime / CPU / GPU)
    + End-user test
 
### 5.3 Project Documentation
- Technical documentation
    + Data flow diagrams
    + Schema definitions
    + ETL job schedules
    + Logging and monitoring strategy
- User guide
- Maintenance procedures

## 6. DataLake - Big Data (Weeks 9-10)
### 6.1 Giới thiệu bigdata, giới thiệu datalake và kiến trúc datalake
- Introduction to Big Data
- DataLake concepts
    + Data storage layer that stores anything
- DataLake architecture
  + Put raw data in.
  + Clean and organize it in layers.
  + Use it later for reports, analysis, or machine learning.

### 6.2 Thiết kế metadata cho datalake, chiến lược ingest data
- Metadata management
- Data ingestion strategies: 
    batch vs stream
- DataLake house concepts
    Lake + warehouse (MSFT Fabrics)
- JSON and XML handling

### 6.3 Pipeline trong bigdata/Data lake
- Pipeline architecture
- Data ingestion patterns
- Data processing workflows

### 6.4 Hadoop HDFS và MapReduce
- Distributed systems overview
    data is split and stored across many computers.
- HDFS architecture
    system that saves big files by splitting them into blocks
- MapReduce programming model
    Map: break into smaller chunks
    Reduce: Combine processed chunks
- HDFS operations
    Upload / Download / Delete / etc

### 6.5 Apache Spark
- Spark architecture
- RDD operations
    + RDD: think tables but written specifically for Spark
    + RDD operations: function written for RDD object
For these part, I can refer to my old implementation in DSCC202, as linked at top page
- Spark SQL
- Spark Streaming
- Structured Streaming


## 7. Big Data Project (Weeks 11-12)

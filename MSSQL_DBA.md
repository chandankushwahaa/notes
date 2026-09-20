# MSSQL 

# SYSTEM DATABASES

## Types of Databases
- **User Databases:** These databases are created by user which have permissions to create a database and can be dropped/altered without  having impact MSSQL server working.
- **System Databases:** These are needed for you SQL server to operate.
	-  These database keeps metadata about MSSQL instance.
	- If you lose a system database like master then SQL server will not function correctly.
	- These databases collectively maintain and manage lot of information about the SQL server  system like logins, databases, jobs, reports, etc.

### 1. master 
- Core system database to manage the SQL server instance.
- It records all the system level information.
- It records the existence of all other databases and the location of those database files.
- Records the initialization information for SQL server. Therefore SQL server cannot start if the master database is unavailable.

**Recommendation for Master Database**
- Always have a current backup of the master database available.
- Do not create user object in master. if do master must be backed up more frequently.

**WHAT to do if master becomes unusable**
- Restore master from a current database backup.
- If you can start the instance, you should be able to restore master from a full database backup.
- Rebuild master completely.
- If server damage to master prevents you from starting SQL server you must rebuild master.


	- **tempdb:** Temporary database to store temporary tables, table variable, cursors, work table, create or rebuild indexes sorted in tempDB.
	- **MSDB:** primary database to manage the SQL server agent configurations.
	- **Resource:** The resource database is responsible for physically storing all of the SQL server system objects.
	- **Model:** template database for all user defined databases.
	- **Distribution:** primary data to support SQL server replication.

# Data Migration
**Data Migration** is the process of moving or copying data from one database or database server to another. The goal is to transfer data while maintaining its integrity and consistency.

### Common Types of Data Migration

* **Same database vendor (Version Upgrade or Server Migration)**

  * Example:

    * SQL Server 2017 → SQL Server 2019
    * SQL Server 2019 (Server A) → SQL Server 2019 (Server B)

* **Different database vendors (Heterogeneous Migration)**

  * Example:

    * SQL Server → PostgreSQL
    * Oracle → MySQL
    * MySQL → SQL Server

### How is Data Migration Achieved?

Data migration can be performed using one or more of the following methods:

* **ETL (Extract, Transform, Load) tools**

  * SQL Server Integration Services (SSIS)
  * Informatica
  * Talend
  * Pentaho

* **Database replication technologies**

  * SQL Server Replication
  * Log Shipping
  * Always On Availability Groups (for specific scenarios)

* **Backup and Restore**

  * Take a backup from the source server and restore it on the destination server.

* **Import/Export utilities**

  * BCP (Bulk Copy Program)
  * SQL Server Import and Export Wizard
  * BULK INSERT

* **Third-party migration tools**

  * Fivetran
  * Qlik Replicate (formerly Attunity)
  * AWS Database Migration Service (DMS)


# Database Migration

**Database Migration** is the process of moving the entire database environment—including the database schema, objects, and often the data—from one environment to another. It may also involve applying schema changes incrementally as an application evolves.

A database migration can include:

* Creating or modifying tables
* Adding or dropping columns
* Creating indexes
* Creating or modifying views
* Stored procedures
* Functions
* Triggers
* Constraints
* Data movement (when required)

### Database Migration Tools

Schema versioning and deployment are commonly managed using:

* Flyway
* Liquibase
* SSDT (SQL Server Data Tools)
* DbUp

These tools integrate with version control systems such as Git and help automate database deployments.


## Is Moving SQL Server 2019 to Another Server Also a Migration?

**Yes.**

Suppose you have:

* SQL Server 2019 on **Server A**
* SQL Server 2019 on **Server B**

If you move or restore your database from Server A to Server B, it is still considered a **database migration**, even though the SQL Server version remains the same.

Examples:

* SQL Server 2019 (Server A) → SQL Server 2019 (Server B) ✔️ Database/Server Migration
* SQL Server 2017 → SQL Server 2019 ✔️ Version Migration
* SQL Server → PostgreSQL ✔️ Heterogeneous Migration
* SQL Server on-premises → Azure SQL Database ✔️ Cloud Migration



---
## BACKUP AND RESTORE
**TYPES of Backups: -**
1. **Full Backup:** a complete, page-be-page copy of an entire database including all the data. All the structures and all other objects stored within the database.

2. **Differential backups:** a backup of a database that has modified in any way since the last full backup.

3. **Transaction log A backup:** of all the committed transactions currently stored in the transaction log.

4. **Copy-only backup:** a special use backup that is independent of the regular sequence of SQL server backups.

5. **File backup:** a backup of one or more database files or filegroups.

6. **Partial backup:** contains data from only some of the filegroups in a database.

**CREATE TEST DATABASE:-**
```
use master
go
```
```
create database backupDB 
go
```
```
use backupDB
go
```
**CREATE TABLE**
```sql
create table Products (ProductID int IDENTITY (1,1) Primary Key, Productflame varchar (100), Brand varchar (100)) 
go
```
**INSERT SOME RECORDS**
```sql
INSERT INTO Products (Productflame, Brand) VALUES ('iPhone 15', 'Apple');
INSERT INTO Products (Productflame, Brand) VALUES ('Galaxy S24', 'Samsung');
INSERT INTO Products (Productflame, Brand) VALUES ('Xperia 1 V', 'Sony');
INSERT INTO Products (Productflame, Brand) VALUES ('Pixel 8', 'Google');
INSERT INTO Products (Productflame, Brand) VALUES ('ThinkPad X1 Carbon', 'Lenovo');
```
View the TABLE to verify the data
```sql
SELECT * FROM Product;
```
### 1. FULL BACKUP USING T-SQL
```sql
BACKUP DATABASE [<DB_NAME>] TO DISK N'd:\fullbackups\dbbackup.bak'
WITH NOINIT, NAME NDB NAME>-Full Database Backup", COMPRESSION STATS 10
go
-- NOINIT: not be overwitten, STATS 10: updated every 10sec
```

**STEP FOR FULL BACKUP GUI:-**
- right click on database ->Tasks->Backup
- Destination: choose location remove and add new location if required followed by file name with .bak extension.
>NOTE: Take backup in off hours because it consume high CPU.
- To see the backup go to the location where you saved it.
>NOTE: after full backup if some entries added or replace and at that time do crash then the changes will not get recovered.

### 2. TRANSACTIONAL LOG BACKUP
To take tran. log backup 1st we must have recovery model is in FULL mode and 2nd full database backup had been executed.
**TO check recovery mode in all the databases:-**
```sql
SELECT name, recovery model desc
FROM sys.databases
ORDER BY name
```
Insert data into table AFTER a full backup, but before a transactional log backup. so it will log the new entry

**Transactional BACKUP USING T-SQL:-**
```sql
BACKUP LOG [<DB_NAME>]
TO DISK N'd:\fullbackups\dbbackup.bak' WITH NOINIT,
NAMEN<DB NAME>-Transactional Log Backup', COMPRESSION
STATS-10
go
```
> NOTE: the same bkp file will have the full backup and transactional log backup.

To see the details:-
```sql
RESTORE HEADERONLY FROM DISK N'd:\fullbackup\dbbackup.bak
```
You will see 2 row one is FULL backup and other is Transaction log backup.

> NOTE: FULL backup type is 1, differential backup type is 5 and trans. log backup type is 2.

### 3. DIFFERENTIAL BACKUP
So inserting data is continuing and if this happens all day and if we take transactional log backup every 15mins than at the end we may have more then 100 transactional log backup to accommodate those change made. 50, differential backup only record the changes since the last database FULL backup and it great for the restore.

**SCRIPT**
```sql
BACKUP DATABASE [<DB_NAME>]
TO DISK N'd: \fullbackups\dbbackup.bak" WITH DIFFERENTIAL, NOINIT, NAME N'DB_NAME>-Transactional Log Backup'
COMPRESSION
STATS 10
go
```
>NOTE: Differential are cumulative in nature and they can get large in size so monitor it. IT is use in LARGE databases.

## RESTORE

Delete the database right click and delete.

right click on Databases-> restore database-> device and add backup path-> select the file and click ok-> you will see all the backups taken like Full, transactional log backup and differential backup.-->Click ok it will restore it.

**TYPES:-**

1. With Recovery

2. With No Recovery

3. Standby Mode
---
title: "Installation Considerations for Microsoft SQL Server and Business Central"
ms.custom: na
ms.date: 10/01/2018
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: article
ms.service: "dynamics365-business-central"
---

# Installation Considerations for Microsoft SQL Server and [!INCLUDE[prodshort](../developer/includes/prodshort.md)]
This article describes the requirements for installing and configuring Microsoft SQL Server to work with [!INCLUDE[prodshort](../developer/includes/prodshort.md)].  

[!INCLUDE[navnow_md](../developer/includes/navnow_md.md)] can run on Microsoft SQL Server and Microsoft Azure SQL Database. For a list of supported editions of SQL Server, see [SQL Server Requirements](system-requirement-business-central.md#SQLReq).  

## Using Microsoft SQL Server

### Storage

Use different disks or disk partitions for the following:
-   Windows operating system.
-   Data files for the system databases.
-   Log files for system and user databases.
-   Data and log files for the TempDB database.

For optimal read/write performance, make sure that disks that are used for SQL Server data files are formatted using 64 KB block size.

### Virus scanning

To help you decide which kind of antivirus software to use on the computers that are running Microsoft SQL Server in your environment, see [How to choose antivirus software to run on computers that are running SQL Server](https://aka.ms/chooseantivirussoftwareforsqlserver).

### Memory

For optimal read performance, maximize the available memory on the server according to the version and edition of SQL Server used. Refer to the SQL Server documentation for maximum values.

### SQL Server components
  
If you are installing Microsoft SQL Server for use with [!INCLUDE[prodshort](../developer/includes/prodshort.md)], then install the following components:  
-   Database Engine Services  
-   Client Tools Connectivity  
-   Management Tools - Complete  

### Setup options for Microsoft SQL Server
  
When you are running Microsoft SQL Server Setup, you must provide additional information. Your responses can affect how you use SQL Server with [!INCLUDE[navnow_md](../developer/includes/navnow_md.md)].  

#### TempDB database configuration

For servers with less than 8 cores, create as many data files for the TempDB database as the number of cores. For servers with more than 8 cores, start with 8 data files, and increment with 4 files at a time, if needed.

Make sure that all data files for the TempDB database are of the same size.

Consider putting data and log files for TempDB on a local SSD drive if you are using SAN storage.

#### Data file and log file configuration

Auto-growth of the database and/or transaction log files in production can degrade performance as all transaction must queue up and wait for SQL Server to grow the file before it can begin to process transactions again. This can create bottlenecks. We strongly recommend growing data and log files during off-peak periods and by 10% to 25% of the current size. We do not recommend disabling “Auto-Grow”, as in an emergency it is still better to have SQL Server to auto-grow files than to run out of disk space and bring the database down.

#### Max degree of parallelism (MAXDOP)

The SQL queries generated by Dynamics NAV is of OLTP type (many, small transactions). It is therefore recommended to run Dynamics NAV   with `MAXDOP` set to the value 1.

On SQL Server 2014, `MAXDOP` can only be set on the instance level, changing an advanced server configuration option. 
On SQL Server 2016, `MAXDOP` can be set on the database level, changing a database scoped configuration. 

Both advanced server configuration options and database scoped configurations can be set by using SQL Server Management Studio, see the SQL Server documentation for details.

> NOTE
> If you are running SQL Server Enterprise Edition, index maintenance can be done in parallel. If you run maintenance jobs to do this work in off-peak hours, you might want to set `MAXDOP` back to 0 while running these jobs. On SQL Server 2016, it is possible to set MAXDOP directly in the Rebuild Index Task wizard.

#### Instance configuration
  
If you plan on installing the [!INCLUDE[prodshort](../developer/includes/prodshort.md)] Demo database, and you want [!INCLUDE[prodshort](../developer/includes/prodshort.md)] Setup to use an already installed version of SQL Server \(and not to install SQL Server Express\), you must create a SQL Server instance named **NAVDEMO** in SQL Server before you run Setup. Otherwise, Setup will install SQL Server Express automatically, even if there is a valid version of SQL Server already on the computer. If you do not plan to install the Demo database, or if you have no objection to using SQL Server Express, you are free to use the **default instance** and **Instance ID** on the **Instance Configuration** page, or to specify any instance name.  

### Database engine service

Each SQL Server instance is run by its own windows service. The following two things are important to configure for these services

#### Startup options

Enable trace flags 1117 and 1118 as startup options for SQL Server 2014. For SQL Server 2016, these trace flags are enabled by default.

Startup options can be set by using SQL Server Configuration Manager, see the SQL Server documentation for details.

#### Service account

We recommend that you use dedicated domain user accounts for the Windows services running your [!INCLUDE[server](../developer/includes/server.md)] instances and your SQL Server instances, instead of a Local System account or the Network Service account.  

The [!INCLUDE[server](../developer/includes/server.md)] account must have privileges on the SQL Server instances and on the Dynamics NAV database(s). See [Provisioning the Server Service Account](provision-server-account.md) for details.

For installations on SQL Server 2014, consider adding the service account for the SQL Server engine to the **Perform Volume Maintenance Tasks** security policy. For SQL Server 2016, it is possible to do this from the installer.

### Database configurations

After Dynamics NAV has been installed, it is important to check a few settings on the Dynamics NAV database(s). This is especially important for databases, which have been upgraded from previous versions of SQL Server.

#### Statistics

The databases used by [!INCLUDE[prodshort](../developer/includes/prodshort.md)] should have set the options AUTO_CREATE_STATISTICS and AUTO_UPDATE_STATISTICS to the value ON (this is the default behavior and should not be changed)

SQL Server (2014 and earlier) uses a threshold based on the percent of rows changed before triggering an update of the statistics for a table regardless of the number of rows in the table. It is possible to change this behaviour by setting trace flag 2371 as a startup option for the instance. See Knowledge Base article ID 2754171, https://support.microsoft.com/en-gb/help/2754171/controlling-autostat-auto-update-statistics-behavior-in-sql-server for more information about when to set this trace flag.

SQL Server (starting with 2016 and under the compatibility level 130) uses a threshold that adjusts according to the number of rows in the table. With this change, statistics on large tables will be updated more often.

Even with "Auto Update Statistics" enabled, we still strongly recommend running a periodic SQL Agent job to update statistics. This is because "Auto Update Statistics" will only be triggered according to the rules described above. On large tables with tens of millions of records (such as Value Entry, Item Ledger Entry and G/L Entry), a small percentage of data in a given statistic such as [Entry No.] can change and have a material effect on the overall data distribution in that statistic. This can cause inefficient query plans, resulting  in degraded query performance until any threshold is reached. We recommend using the T-SQL procedure "sp_updatestats" to update statistics, as it will only update statistics where data has been changed. We recommend creating a SQL Agent Job that runs daily or weekly (depending on transaction volume) during off-peak hours to update all statistics where data has changed.

#### Other database options

We recommend to set the database option PAGE_VERIFY to the value CHECKSUM for all databases (including TEMPDB) as this is the most robust method of detecting physical database corruption. This is the default setting for new installations.


### Backup

Remember to setup backup of both system and user databases. Remember also to test restore procedures regularly.

## Using Microsoft Azure SQL Database
  
You can deploy a [!INCLUDE[prodshort](../developer/includes/prodshort.md)] database to Azure SQL Database. Azure SQL Database is a cloud service that provides data storage as a part of the Azure Services Platform.  

 To optimize performance, we recommend that the [!INCLUDE[server](../developer/includes/server.md)] instance that connects to the database is also deployed on a virtual machine in Azure. Additionally, the virtual machine and SQL Database must be in the same Azure region.  

 For development and maintenance work on [!INCLUDE[prodshort](../developer/includes/prodshort.md)] applications, if the [!INCLUDE[nav_dev_long](../developer/includes/nav_dev_long_md.md)] is installed on the same virtual machine in Azure as the [!INCLUDE[server](../developer/includes/server.md)], then you can connect to the Azure SQL database from the [!INCLUDE[nav_dev_short](../developer/includes/nav_dev_short_md.md)].  

 For more information, see [Deploying a Business Central Database to Azure SQL Database](deploy-database-azure-sql-database.md).  

## Data Encryption between [!INCLUDE[server](../developer/includes/server.md)] and SQL Server  
 When SQL Server and [!INCLUDE[server](../developer/includes/server.md)] are running on different computers, you can make this data channel more secure by encrypting the connection with IPSec. \(Other encryption options are not supported.\) For information on how to do this, see [Encrypting Connections to SQL Server](http://go.microsoft.com/fwlink/?LinkId=147732), which is part of SQL Server 2008 Books Online in MSDN library.  

## See Also  
 [Data Access](../administration/optimize-sql-data-access.md)    
 [Troubleshooting: SQL Server Connection Problems](../administration/Troubleshooting-SQL-Server-Connection-Problems.md)   
 [Deployment](Deployment.md)  
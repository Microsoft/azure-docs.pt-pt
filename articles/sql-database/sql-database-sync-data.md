---
title: Sincronização de Dados
description: This overview introduces Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 1ee2efbb8aebfc2f1a94c89edef6166898946d8a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422520"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sync data across multiple cloud and on-premises databases with SQL Data Sync

SQL Data Sync is a service built on Azure SQL Database that lets you synchronize the data you select bi-directionally across multiple SQL databases and SQL Server instances.

> [!IMPORTANT]
> Azure SQL Data Sync does not support Azure SQL Database Managed Instance at this time.

## <a name="when-to-use-data-sync"></a>When to use Data Sync

Data Sync is useful in cases where data needs to be kept updated across several Azure SQL databases or SQL Server databases. Here are the main use cases for Data Sync:

- **Hybrid Data Synchronization:** With Data Sync, you can keep data synchronized between your on-premises databases and Azure SQL databases to enable hybrid applications. This capability may appeal to customers who are considering moving to the cloud and would like to put some of their application in Azure.
- **Distributed Applications:** In many cases, it's beneficial to separate different workloads across different databases. For example, if you have a large production database, but you also need to run a reporting or analytics workload on this data, it's helpful to have a second database for this additional workload. This approach minimizes the performance impact on your production workload. You can use Data Sync to keep these two databases synchronized.
- **Globally Distributed Applications:** Many businesses span several regions and even several countries/regions. To minimize network latency, it's best to have your data in a region close to you. With Data Sync, you can easily keep databases in regions around the world synchronized.

Data Sync isn't the preferred solution for the following scenarios:

| Cenário | Some recommended solutions |
|----------|----------------------------|
| Recuperação Após Desastre | [Azure geo-redundant backups](sql-database-automated-backups.md) |
| Read Scale | [Use read-only replicas to load balance read-only query workloads (preview)](sql-database-read-scale-out.md) |
| ETL (OLTP to OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) or [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migration from on-premises SQL Server to Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Overview of SQL Data Sync

Data Sync is based around the concept of a Sync Group. A Sync Group is a group of databases that you want to synchronize.

Data Sync uses a hub and spoke topology to synchronize data. You define one of the databases in the sync group as the Hub Database. The rest of the databases are member databases. Sync occurs only between the Hub and individual members.

- The **Hub Database** must be an Azure SQL Database.
- The **member databases** can be either SQL Databases, on-premises SQL Server databases, or SQL Server instances on Azure virtual machines.
- The **Sync Database** contains the metadata and log for Data Sync. The Sync Database has to be an Azure SQL Database located in the same region as the Hub Database. The Sync Database is customer created and customer owned.

> [!NOTE]
> If you're using an on premises database as a member database, you have to [install and configure a local sync agent](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sync data between databases](media/sql-database-sync-data/sync-data-overview.png)

A Sync Group has the following properties:

- The **Sync Schema** describes which data is being synchronized.
- The **Sync Direction** can be bi-directional or can flow in only one direction. That is, the Sync Direction can be *Hub to Member*, or *Member to Hub*, or both.
- The **Sync Interval** describes how often synchronization occurs.
- The **Conflict Resolution Policy** is a group level policy, which can be *Hub wins* or *Member wins*.

## <a name="how-does-data-sync-work"></a>How does Data Sync work

- **Tracking data changes:** Data Sync tracks changes using insert, update, and delete triggers. The changes are recorded in a side table in the user database. Note that BULK INSERT doesn't fire triggers by default. If FIRE_TRIGGERS isn't specified, no insert triggers execute. Add the FIRE_TRIGGERS option so Data Sync can track those inserts. 
- **Synchronizing data:** Data Sync is designed in a Hub and Spoke model. The Hub syncs with each member individually. Changes from the Hub are downloaded to the member and then changes from the member are uploaded to the Hub.
- **Resolving conflicts:** Data Sync provides two options for conflict resolution, *Hub wins* or *Member wins*.
  - If you select *Hub wins*, the changes in the hub always overwrite changes in the member.
  - If you select *Member wins*, the changes in the member overwrite changes in the hub. If there's more than one member, the final value depends on which member syncs first.

## <a name="compare-data-sync-with-transactional-replication"></a>Compare Data Sync with Transactional Replication

| | Sincronização de Dados | Replicação Transacional |
|---|---|---|
| Advantages | - Active-active support<br/>- Bi-directional between on-premises and Azure SQL Database | - Lower latency<br/>- Transactional consistency<br/>- Reuse existing topology after migration |
| Disadvantages | - 5 min or more latency<br/>- No transactional consistency<br/>- Higher performance impact | - Can’t publish from Azure SQL Database single database or pooled database<br/>- High maintenance cost |

## <a name="get-started-with-sql-data-sync"></a>Introdução à Sincronização de Dados do SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Set up Data Sync in the Azure portal

- [Configurar a Sincronização de Dados SQL do Azure](sql-database-get-started-sql-data-sync.md)
- Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>Set up Data Sync with PowerShell

- [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
- [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Review the best practices for Data Sync

- [Melhores práticas da Sincronização de Dados SQL do Azure](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Did something go wrong

- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Consistency and performance

### <a name="eventual-consistency"></a>Consistência eventual

Since Data Sync is trigger-based, transactional consistency isn't guaranteed. Microsoft guarantees that all changes are made eventually and that Data Sync doesn't cause data loss.

### <a name="performance-impact"></a>Performance impact

Data Sync uses insert, update, and delete triggers to track changes. It creates side tables in the user database for change tracking. These change tracking activities have an impact on your database workload. Assess your service tier and upgrade if needed.

Provisioning and deprovisioning during sync group creation, update, and deletion may also impact the database performance.

## <a name="sync-req-lim"></a> Requirements and limitations

### <a name="general-requirements"></a>Requisitos gerais

- Each table must have a primary key. Don't change the value of the primary key in any row. If you have to change a primary key value, delete the row and recreate it with the new primary key value.

> [!IMPORTANT]
> Changing the value of an existing primary key will result in the following faulty behavior:
> - Data between hub and member can be lost even though sync does not report any issue.
> - Sync can fail because the tracking table has a non-existing row from source due to the primary key change.

- O isolamento de instantâneos tem de estar ativado. Para obter mais informações, veja [Snapshot Isolation in SQL Server (Isolamento de Instantâneo no SQL Server)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitações gerais

- A table can't have an identity column that isn't the primary key.
- A primary key can't have the following data types: sql_variant, binary, varbinary, image, xml.
- Be cautious when you use the following data types as a primary key, because the supported precision is only to the second: time, datetime, datetime2, datetimeoffset.
- The names of objects (databases, tables, and columns) can't contain the printable characters period (.), left square bracket ([), or right square bracket (]).
- Azure Active Directory authentication isn't supported.
- Tables with same name but different schema (for example, dbo.customers and sales.customers) aren't supported.
- Columns with User Defined Data Types aren't supported

#### <a name="unsupported-data-types"></a>Unsupported data types

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection (XML supported)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Unsupported column types

Data Sync can't sync read-only or system-generated columns. Por exemplo:

- Computed columns.
- System-generated columns for temporal tables.

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitations on service and database dimensions

| **Dimensions**                                                  | **Limite**              | **Solução**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximum number of sync groups any database can belong to.       | 5                      |                             |
| Maximum number of endpoints in a single sync group              | 30                     |                             |
| Maximum number of on-premises endpoints in a single sync group. | 5                      | Create multiple sync groups |
| Database, table, schema, and column names                       | 50 characters per name |                             |
| Tables in a sync group                                          | 500                    | Create multiple sync groups |
| Columns in a table in a sync group                              | 1000                   |                             |
| Data row size on a table                                        | 24 Mb                  |                             |
| Minimum sync interval                                           | 5 Minutos              |                             |

> [!NOTE]
> There may be up to 30 endpoints in a single sync group if there is only one sync group. If there is more than one sync group, the total number of endpoints across all sync groups cannot exceed 30. If a database belongs to multiple sync groups, it is counted as multiple endpoints, not one.

## <a name="faq-about-sql-data-sync"></a>FAQ about SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>How much does the SQL Data Sync service cost

There's no charge for the SQL Data Sync service itself. However, you still collect data transfer charges for data movement in and out of your SQL Database instance. For more info, see [SQL Database pricing](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>What regions support Data Sync

SQL Data Sync is available in all regions.

### <a name="is-a-sql-database-account-required"></a>Is a SQL Database account required

Sim. You must have a SQL Database account to host the Hub Database.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Can I use Data Sync to sync between SQL Server on-premises databases only

Not directly. You can sync between SQL Server on-premises databases indirectly, however, by creating a Hub database in Azure, and then adding the on-premises databases to the sync group.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Can I use Data Sync to sync between SQL Databases that belong to different subscriptions

Sim. You can sync between SQL Databases that belong to resource groups owned by different subscriptions.

- If the subscriptions belong to the same tenant, and you have permission to all subscriptions, you can configure the sync group in the Azure portal.
- Otherwise, you have to use PowerShell to add the sync members that belong to different subscriptions.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Can I use Data Sync to sync between SQL Databases that belong to different clouds (like Azure Public Cloud and Azure China 21Vianet)

Sim. You can sync between SQL Databases that belong to different clouds, you have to use PowerShell to add the sync members that belong to the different subscriptions.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Can I use Data Sync to seed data from my production database to an empty database, and then sync them

Sim. Create the schema manually in the new database by scripting it from the original. After you create the schema, add the tables to a sync group to copy the data and keep it synced.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Should I use SQL Data Sync to back up and restore my databases

It isn't recommended to use SQL Data Sync to create a backup of your data. You can't back up and restore to a specific point in time because SQL Data Sync synchronizations are not versioned. Furthermore, SQL Data Sync does not back up other SQL objects, such as stored procedures, and doesn't do the equivalent of a restore operation quickly.

For one recommended backup technique, see [Copy an Azure SQL database](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Can Data Sync sync encrypted tables and columns

- If a database uses Always Encrypted, you can sync only the tables and columns that are *not* encrypted. You can't sync the encrypted columns, because Data Sync can't decrypt the data.
- If a column uses Column-Level Encryption (CLE), you can sync the column, as long as the row size is less than the maximum size of 24 Mb. Data Sync treats the column encrypted by key (CLE) as normal binary data. To decrypt the data on other sync members, you need to have the same certificate.

### <a name="is-collation-supported-in-sql-data-sync"></a>Is collation supported in SQL Data Sync

Sim. SQL Data Sync supports collation in the following scenarios:

- If the selected sync schema tables aren't already in your hub or member databases, then when you deploy the sync group, the service automatically creates the corresponding tables and columns with the collation settings selected in the empty destination databases.
- If the tables to be synced already exist in both your hub and member databases, SQL Data Sync requires that the primary key columns have the same collation between hub and member databases to successfully deploy the sync group. There are no collation restrictions on columns other than the primary key columns.

### <a name="is-federation-supported-in-sql-data-sync"></a>Is federation supported in SQL Data Sync

Federation Root Database can be used in the SQL Data Sync Service without any limitation. You can't add the Federated Database endpoint to the current version of SQL Data Sync.

## <a name="next-steps"></a>Passos seguintes

### <a name="update-the-schema-of-a-synced-database"></a>Update the schema of a synced database

Do you have to update the schema of a database in a sync group? Schema changes aren't automatically replicated. For some solutions, see the following articles:

- [Automate the replication of schema changes in Azure SQL Data Sync](sql-database-update-sync-schema.md)
- [Use PowerShell to update the sync schema in an existing sync group](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorizar e resolver problemas

Is SQL Data Sync doing as expected? To monitor activity and troubleshoot issues, see the following articles:

- [Monitor Azure SQL Data Sync with Azure Monitor logs](sql-database-sync-monitor-oms.md)
- [Resolver problemas da Sincronização de Dados SQL do Azure](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Saiba mais sobre a Base de Dados SQL do Azure

For more info about SQL Database, see the following articles:

- [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)

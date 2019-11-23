---
title: Data Warehouse Units (DWUs) in Azure Synapse Analytics (formerly SQL DW)
description: Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7cd6a037f339f193f63cbe152f0ea9964679c231
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420489"
---
# <a name="data-warehouse-units-dwus"></a>Data Warehouse Units (DWUs)

Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.

## <a name="what-are-data-warehouse-units"></a>What are Data Warehouse Units

A [SQL pool](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) represents a collection of analytic resources that are being provisioned when using [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Analytic resources are defined as a combination of CPU, memory and IO. These three resources are bundled into units of compute scale called Data Warehouse Units (DWUs). A DWU represents an abstract, normalized measure of compute resources and performance. A change to your service level alters the number of DWUs that are available to the system, which in turn adjusts the performance, and the cost, of your system.

For higher performance, you can increase the number of data warehouse units. For less performance, reduce data warehouse units. Storage and compute costs are billed separately, so changing data warehouse units does not affect storage costs.

Performance for data warehouse units is based on these workload metrics:

- How fast a standard data warehousing query can scan a large number of rows and then perform a complex aggregation. This operation is I/O and CPU intensive.
- How fast the data warehouse can ingest data from Azure Storage Blobs or Azure Data Lake. This operation is network and CPU intensive.
- How fast the [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL command can copy a table. This operation involves reading data from storage, distributing it across the nodes of the appliance and writing to storage again. This operation is CPU, IO, and network intensive.

Increasing DWUs:

- Linearly changes performance of the system for scans, aggregations, and CTAS statements
- Increases the number of readers and writers for PolyBase load operations
- Increases the maximum number of concurrent queries and concurrency slots.

## <a name="service-level-objective"></a>Service Level Objective

The Service Level Objective (SLO) is the scalability setting that determines the cost and performance level of your data warehouse. The service levels for Gen2 SQL pool are measured in data warehouse units (DWU), for example DW2000c.

In T-SQL, the SERVICE_OBJECTIVE setting determines the service level for your SQL pool.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Limites de capacidade

Each SQL server (for example, myserver.database.windows.net) has a [Database Transaction Unit (DTU)](../sql-database/sql-database-what-is-a-dtu.md) quota that allows a specific number of data warehouse units. For more information, see the [workload management capacity limits](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>How many data warehouse units do I need

The ideal number of data warehouse units depends very much on your workload and the amount of data you have loaded into the system.

Steps for finding the best DWU for your workload:

1. Begin by selecting a smaller DWU.
2. Monitor your application performance as you test data loads into the system, observing the number of DWUs selected compared to the performance you observe.
3. Identify any additional requirements for periodic periods of peak activity. Workloads that show significant peaks and troughs in activity may need to be scaled frequently.

SQL Analytics is a scale-out system that can provision vast amounts of compute and query sizeable quantities of data. To see its true capabilities for scaling, especially at larger DWUs, we recommend scaling the data set as you scale to ensure that you have enough data to feed the CPUs. For scale testing, we recommend using at least 1 TB.

> [!NOTE]
>
> Query performance only increases with more parallelization if the work can be split between compute nodes. If you find that scaling is not changing your performance, you may need to tune your table design and/or your queries. For query tuning guidance, see [Manage user queries](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Permissões

Changing the data warehouse units requires the permissions described in [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

Built-in roles for Azure resources such as SQL DB Contributor and SQL Server Contributor can change DWU settings.

## <a name="view-current-dwu-settings"></a>View current DWU settings

To view the current DWU setting:

1. Open SQL Server Object Explorer in Visual Studio.
2. Connect to the master database associated with the logical SQL Database server.
3. Select from the sys.database_service_objectives dynamic management view. Segue-se um exemplo:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Change data warehouse units

### <a name="azure-portal"></a>Portal do Azure

To change DWUs:

1. Open the [Azure portal](https://portal.azure.com), open your database, and click **Scale**.

2. Under **Scale**, move the slider left or right to change the DWU setting.

3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **sim** para confirmar ou **não** para cancelar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

To change the DWUs, use the [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet. The following example sets the service level objective to DW1000c for the database MySQLDW that is hosted on server MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

For more information, see [PowerShell cmdlets for SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

With T-SQL you can view the current DWU settings, change the settings, and check the progress.

To change the DWUs:

1. Connect to the master database associated with your logical SQL Database server.
2. Use the [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL statement. The following example sets the service level objective to DW1000c for the database MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>APIs REST

To change the DWUs, use the [Create or Update Database](/rest/api/sql/databases/createorupdate) REST API. The following example sets the service level objective to DW1000c for the database MySQLDW, which is hosted on server MyServer. The server is in an Azure resource group named ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

For more REST API examples, see [REST APIs for SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Check status of DWU changes

DWU changes may take several minutes to complete. If you are scaling automatically, consider implementing logic to ensure that certain operations have been completed before proceeding with another action.

Checking the database state through various endpoints allows you to correctly implement automation. The portal provides notification upon completion of an operation and the databases current state but does not allow for programmatic checking of state.

You cannot check the database state for scale-out operations with the Azure portal.

To check the status of DWU changes:

1. Connect to the master database associated with your logical SQL Database server.

1. Submit the following query to check database state.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. Submit the following query to check status of operation

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
This DMV returns information about various management operations on your SQL pool such as the operation and the state of the operation, which is either IN_PROGRESS or COMPLETED.

## <a name="the-scaling-workflow"></a>The scaling workflow

When you start a scale operation, the system first kills all open sessions, rolling back any open transactions to ensure a consistent state. For scale operations, scaling only occurs after this transactional rollback has completed.  

- For a scale-up operation, the system detaches all compute nodes, provisions the additional compute nodes, and then reattaches to the storage layer.
- For a scale-down operation, the system detaches all compute nodes and then reattaches only the needed nodes to the storage layer.

## <a name="next-steps"></a>Passos seguintes

To learn more about managing performance, see [Resource classes for workload management](resource-classes-for-workload-management.md) and [Memory and concurrency limits](memory-concurrency-limits.md).

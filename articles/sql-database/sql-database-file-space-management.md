---
title: Gestão de espaços de ficheiros do Azure SQL Database única/em pool bases de dados | Documentos da Microsoft
description: Esta página descreve como gerir o espaço de ficheiro com bases de dados únicos e agrupados na base de dados do Azure SQL e fornece exemplos de código para saber como determinar se necessita de reduzir um único ou uma base de dados agrupado, bem como a forma como para executar uma base de dados reduzir a operação.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
manager: craigg
ms.date: 03/07/2019
ms.openlocfilehash: a6fccb087d8a2c78bb1ad47758952b76b245dbde
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576213"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Gerir o espaço de ficheiro para bases de dados individuais e agrupados na base de dados do Azure SQL

Este artigo descreve os diferentes tipos de espaço de armazenamento para bases de dados individuais e agrupados na base de dados do Azure SQL e os passos que podem ser realizados quando o espaço de ficheiro atribuídos para bases de dados e precisa ser gerenciado explicitamente de conjuntos elásticos.

> [!NOTE]
> Este artigo não se aplica à opção de implementação de instância gerida na base de dados do Azure SQL.

## <a name="overview"></a>Descrição geral

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o únicos e em pool bases de dados na base de dados do Azure SQL, existem padrões de carga de trabalho em que a alocação de arquivos de dados subjacentes para bases de dados pode tornar-se maior do que a quantidade de páginas de dados utilizados. Esta condição pode ocorrer se o espaço utilizado aumentar e se os dados forem eliminados subsequentemente. O motivo é que o espaço de ficheiro alocado não será recuperado automaticamente quando os dados serão eliminados.

Nos cenários abaixo, monitorizar a utilização do espaço de ficheiros e encolher os ficheiros de dados poderá ser necessário:

- Permita o crescimento dos dados num conjunto elástico quando o espaço de ficheiros alocado às respetivas bases de dados atingir o tamanho máximo do conjunto.
- Permita a diminuição do tamanho máximo de uma base de dados individual ou de um conjunto elástico.
- Permita a alteração de uma base de dados individual ou de um conjunto elástico para outro escalão de serviço ou escalão de desempenho com um tamanho máximo mais baixo.

### <a name="monitoring-file-space-usage"></a>Monitorizar a utilização do espaço de ficheiro

A maioria das métricas de espaço de armazenamento apresentadas no portal do Azure e as seguintes APIs apenas medem o tamanho das páginas de dados utilizados:

- O Azure Resource Manager com base em métricas de APIs incluindo PowerShell [get-métricas](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

No entanto, as seguintes APIs também medem o tamanho do espaço alocado para bases de dados e elastic pools:

- T-SQL:  [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Reduzindo ficheiros de dados

O serviço de base de dados SQL não reduzir automaticamente os ficheiros de dados para recuperar espaço alocado não utilizado devido ao impacto potencial para desempenho da base de dados.  No entanto, os clientes podem reduzir os ficheiros de dados por meio de Self-Service num momento à sua escolha, seguindo os passos descritos em [recuperar não utilizados alocado espaço](#reclaim-unused-allocated-space).

> [!NOTE]
> Ao contrário dos arquivos de dados, o serviço de base de dados SQL diminui automaticamente ficheiros de registo, uma vez que essa operação não afeta o desempenho de base de dados. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Compreender os tipos de espaço de armazenamento para uma base de dados

Noções básicas sobre as seguintes quantidades de espaço de armazenamento são importantes para gerir o espaço de ficheiro das bases de dados.

|Quantidade de base de dados|Definição|Comentários|
|---|---|---|
|**Espaço de dados utilizado**|A quantidade de espaço utilizado para armazenar dados de base de dados em páginas de 8 KB.|Em geral, o espaço usado aumenta (diminuições) no inserções (eliminações). Em alguns casos, o espaço utilizado não é alterado no inserções ou elimina dependendo da quantidade e o padrão de dados envolvidos na operação e qualquer fragmentação. Por exemplo, a eliminar uma linha de cada página de dados não necessariamente reduzir o espaço utilizado.|
|**Espaço de dados alocado**|A quantidade de formatado disponibilizado para armazenar dados de base de dados de espaço de ficheiro.|A quantidade de espaço alocado cresce automaticamente, mas nunca diminui após eliminações. Esse comportamento garante que futuras inserções são mais rápidas, uma vez que o espaço não precisa de ser reformatado.|
|**Espaço de dados alocado, mas não utilizado**|A diferença entre a quantidade de espaço de dados alocado e espaço de dados utilizado.|Esta quantidade representa a quantidade máxima de espaço livre que pode ser recuperada pelo reduzindo ficheiros de dados de base de dados.|
|**Tamanho máximo de dados**|A quantidade máxima de espaço que pode ser utilizada para armazenar dados de base de dados.|A quantidade de espaço de dados alocado não pode ultrapassar o tamanho máximo de dados.|
||||

O diagrama seguinte ilustra a relação entre os diferentes tipos de espaço de armazenamento para uma base de dados.

![tipos de espaço de armazenamento e as relações](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Consultar uma base de dados para obter informações de espaço de armazenamento

As seguintes consultas podem ser utilizadas para determinar as quantidades de espaço de armazenamento para uma base de dados.  

### <a name="database-data-space-used"></a>Espaço de dados de base de dados utilizado

Modifique a consulta seguinte para devolver a quantidade de espaço de dados de base de dados utilizado.  As unidades do resultado da consulta são em MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados de base de dados alocados e o espaço alocado não utilizado

Utilize a seguinte consulta para devolver a quantidade de espaço de dados de base de dados alocado e a quantidade de espaço não utilizado alocado.  As unidades do resultado da consulta são em MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>Tamanho máximo de dados de base de dados

Modifique a consulta seguinte para devolver o tamanho máximo de dados de base de dados.  As unidades do resultado da consulta são em bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Compreender os tipos de espaço de armazenamento para um conjunto elástico

Noções básicas sobre as seguintes quantidades de espaço de armazenamento são importantes para gerir o espaço de ficheiro de um conjunto elástico.

|Quantidade de conjunto elástico|Definição|Comentários|
|---|---|---|
|**Espaço de dados utilizado**|A soma do espaço de dados utilizada por todas as bases de dados do conjunto elástico.||
|**Espaço de dados alocado**|A soma do espaço de dados alocado por todas as bases de dados do conjunto elástico.||
|**Espaço de dados alocado, mas não utilizado**|A diferença entre a quantidade de espaço de dados alocado e espaço de dados utilizado por todas as bases de dados do conjunto elástico.|Esta quantidade representa a quantidade máxima de espaço alocado para o conjunto elástico que pode ser recuperado pelo reduzindo ficheiros de dados de base de dados.|
|**Tamanho máximo de dados**|A quantidade máxima de espaço de dados que pode ser utilizado pelo conjunto elástico para todas as suas bases de dados.|O espaço alocado para o conjunto elástico não deve exceder o tamanho máximo do conjunto elástico.  Se esta condição ocorre, o espaço alocado, que é utilizado pode ser recuperado reduzindo ficheiros de dados de base de dados.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Consultar um conjunto elástico para obter informações de espaço de armazenamento

As seguintes consultas podem ser utilizadas para determinar as quantidades de espaço de armazenamento para um conjunto elástico.  

### <a name="elastic-pool-data-space-used"></a>Espaço de dados do conjunto elástico utilizado

Modifique a consulta seguinte para devolver a quantidade de espaço de dados do conjunto elástico utilizado.  As unidades do resultado da consulta são em MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados do conjunto elástico atribuído e o espaço alocado não utilizado

Modifique o seguinte script do PowerShell para devolver uma tabela que lista o espaço alocado e não utilizado de espaço alocado para cada base de dados num conjunto elástico. A tabela orders bases de dados dessas bases de dados com a maior quantidade não utilizada do alocado espaço para a menor quantidade não utilizada de espaço em atribuído.  As unidades do resultado da consulta são em MB.  

Os resultados da consulta para determinar o espaço alocado para cada base de dados no agrupamento pode ser adicionado em conjunto para determinar o total de espaço alocado para o conjunto elástico. O espaço do conjunto elástico alocado não deve exceder o tamanho máximo do conjunto elástico.  

O script do PowerShell requer o módulo do PowerShell do SQL Server – ver [módulo do PowerShell transferir](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) para instalar.

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

Captura de ecrã seguinte é um exemplo da saída do script:

![conjunto elástico alocado espaço e exemplo de espaço alocado não utilizado](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Tamanho máximo do conjunto elástico dados

Modifique a consulta de T-SQL seguinte para devolver o tamanho máximo de dados do conjunto elástico.  As unidades do resultado da consulta são em MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Recuperar espaço alocado não utilizado

### <a name="dbcc-shrink"></a>Redução DBCC

Depois de bases de dados tenham sido identificados para reclamação de espaço alocado não utilizado, modifique o nome da base de dados no comando seguinte para reduzir os ficheiros de dados para cada base de dados.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Este comando pode afetar o desempenho de base de dados enquanto está em execução e, se possível, deve ser executado durante períodos de baixa utilização.  

Para obter mais informações sobre este comando, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

### <a name="auto-shrink"></a>Redução automática

Em alternativa, a redução automática pode ser ativada para uma base de dados.  Redução automática reduz a complexidade da gestão de ficheiros e menos causa um impacto no desempenho do banco de dados que `SHRINKDATABASE` ou `SHRINKFILE`.  Redução automática pode ser particularmente útil para gerir conjuntos elásticos com muitas bases de dados.  No entanto, a redução automática pode ser menos eficiente reclamação de espaço de ficheiro que `SHRINKDATABASE` e `SHRINKFILE`.
Para ativar a redução automática, modifique o nome da base de dados no comando seguinte.


```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Para obter mais informações sobre este comando, consulte [base de dados definida](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=sql-server-2017) opções. 

### <a name="rebuild-indexes"></a>Reconstruir índices

Depois de arquivos de dados do banco de dados são Encolher, os índices podem se tornar fragmentados e perdem a eficácia de otimização de desempenho. Se ocorrer uma degradação do desempenho, em seguida, considere reconstruir índices de base de dados. Para obter mais informações sobre a fragmentação e reconstruir índices, consulte [Reorganize e reconstruir índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Passos Seguintes

- Para informações sobre os tamanhos de máximo de base de dados, consulte:
  - [Base de dados do SQL do Azure limites de modelo para uma base de dados de compra baseado em vCore](sql-database-vcore-resource-limits-single-databases.md)
  - [Limites de recursos para bases de dados individuais com o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-single-databases.md)
  - [Base de dados do SQL do Azure limites de modelo para conjuntos elásticos de compra baseado em vCore](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Limites de recursos para conjuntos elásticos com o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para obter mais informações sobre o `SHRINKDATABASE` comando este problema, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Para obter mais informações sobre a fragmentação e reconstruir índices, consulte [Reorganize e reconstruir índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

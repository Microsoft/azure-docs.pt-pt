---
title: Gerenciamento de espaço de arquivo de bancos de dados individuais/em pool
description: Esta página descreve como gerenciar o espaço de arquivo com bancos de dados individuais e em pool no banco de dados SQL do Azure e fornece exemplos de código para determinar se você precisa reduzir um banco de dados único ou em pool, além de como executar uma operação de redução de banco de dados.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 007bbffbd7c4fcad339f88eb78991eb39fb829e6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420977"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Gerenciar o espaço de arquivo para bancos de dados individuais e em pool no banco de dados SQL do Azure

Este artigo descreve os diferentes tipos de espaço de armazenamento para bancos de dados individuais e em pool no banco de dados SQL do Azure e as etapas que podem ser executadas quando o espaço de arquivo alocado para bancos de dados e pools elásticos precisa ser gerenciado explicitamente.

> [!NOTE]
> Este artigo não se aplica à opção de implantação de instância gerenciada no banco de dados SQL do Azure.

## <a name="overview"></a>Descrição geral

Com bancos de dados únicos e em pool no Azure SQL Database, há padrões de carga de trabalho em que a alocação de arquivos subjacentes para bancos de dados pode se tornar maior do que a quantidade de páginas de dados usadas. Esta condição pode ocorrer se o espaço utilizado aumentar e se os dados forem eliminados subsequentemente. O motivo é que o espaço de arquivo alocado não é recuperado automaticamente quando os dados são excluídos.

Nos cenários abaixo, monitorizar a utilização do espaço de ficheiros e encolher os ficheiros de dados poderá ser necessário:

- Permita o crescimento dos dados num conjunto elástico quando o espaço de ficheiros alocado às respetivas bases de dados atingir o tamanho máximo do conjunto.
- Permita a diminuição do tamanho máximo de uma base de dados individual ou de um conjunto elástico.
- Permita a alteração de uma base de dados individual ou de um conjunto elástico para outro escalão de serviço ou escalão de desempenho com um tamanho máximo mais baixo.

### <a name="monitoring-file-space-usage"></a>Uso do espaço de arquivo de monitoramento

A maioria das métricas de espaço de armazenamento exibidas no portal do Azure e as seguintes APIs medem apenas o tamanho das páginas de dados usadas:

- APIs de métricas baseadas em Azure Resource Manager, incluindo o PowerShell [Get-métricas](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric)
- T-SQL: [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

No entanto, as seguintes APIs também medem o tamanho do espaço alocado para bancos de dados e pools elásticos:

- T-SQL: [Sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Reduzindo arquivos de dados

O serviço do banco de dados SQL não reduz automaticamente os arquivos para recuperar o espaço alocado não utilizado devido ao impacto potencial no desempenho do banco de dado.  No entanto, os clientes podem reduzir os arquivos de dados por meio de autoatendimento em um determinado momento de sua escolha seguindo as etapas descritas em [recuperar espaço alocado não utilizado](#reclaim-unused-allocated-space).

> [!NOTE]
> Ao contrário dos arquivos de dados, o serviço de banco de dados SQL reduz automaticamente os arquivos de log, pois essa operação não afeta o desempenho do banco de dado.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Noções básicas sobre tipos de espaço de armazenamento para um banco de dados

Compreender as quantidades de espaço de armazenamento a seguir é importante para gerenciar o espaço de arquivo de um banco de dados.

|Quantidade de banco de dados|Definição|Comentários|
|---|---|---|
|**Espaço de dados usado**|A quantidade de espaço usada para armazenar dados de banco de dado em páginas de 8 KB.|Geralmente, o espaço usado aumenta (diminui) em inserções (exclusões). Em alguns casos, o espaço usado não é alterado em inserções ou exclusões, dependendo da quantidade e do padrão de dados envolvidos na operação e em qualquer fragmentação. Por exemplo, a exclusão de uma linha de cada página de dados não diminui necessariamente o espaço usado.|
|**Espaço de dados alocado**|A quantidade de espaço em arquivo formatado disponibilizada para armazenar dados de banco de dados.|A quantidade de espaço alocado aumenta automaticamente, mas nunca diminui após as exclusões. Esse comportamento garante que as inserções futuras sejam mais rápidas, pois o espaço não precisa ser reformatado.|
|**Espaço de dados alocado, mas não usado**|A diferença entre a quantidade de espaço de dados alocada e o espaço de dados usado.|Essa quantidade representa a quantidade máxima de espaço livre que pode ser recuperada por meio da redução de arquivos de dados de banco de dado.|
|**Tamanho máximo de dados**|A quantidade máxima de espaço que pode ser usada para armazenar dados de banco de dados.|A quantidade de espaço de dados alocado não pode crescer além do tamanho máximo dos dados.|

O diagrama a seguir ilustra a relação entre os diferentes tipos de espaço de armazenamento para um banco de dados.

![tipos e relações de espaço de armazenamento](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Consultar um único banco de dados para obter informações de espaço de armazenamento

As consultas a seguir podem ser usadas para determinar as quantidades de espaço de armazenamento para um único banco de dados.  

### <a name="database-data-space-used"></a>Espaço de dados usado

Modifique a consulta a seguir para retornar a quantidade de espaço de dados de banco de dado usada.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados alocados e espaço alocado não utilizado

Use a consulta a seguir para retornar a quantidade de espaço de dados alocada e a quantidade de espaço não utilizado alocada.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Tamanho máximo de dados do banco

Modifique a consulta a seguir para retornar o tamanho máximo dos dados do banco.  As unidades do resultado da consulta estão em bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Noções básicas sobre tipos de espaço de armazenamento para um pool elástico

Compreender as quantidades de espaço de armazenamento a seguir é importante para gerenciar o espaço de arquivo de um pool elástico.

|Quantidade do pool elástico|Definição|Comentários|
|---|---|---|
|**Espaço de dados usado**|A soma do espaço de dados usado por todos os bancos de dado no pool elástico.||
|**Espaço de dados alocado**|A soma do espaço de dados alocada por todos os bancos de dado no pool elástico.||
|**Espaço de dados alocado, mas não usado**|A diferença entre a quantidade de espaço de dados alocada e o espaço de dados usados por todos os bancos de dado no pool elástico.|Essa quantidade representa a quantidade máxima de espaço alocada para o pool elástico que pode ser recuperada por meio da redução de arquivos de dados de banco de dado.|
|**Tamanho máximo de dados**|A quantidade máxima de espaço de dados que pode ser usada pelo pool elástico para todos os seus bancos.|O espaço alocado para o pool elástico não deve exceder o tamanho máximo do pool elástico.  Se essa condição ocorrer, o espaço alocado que não é usado pode ser recuperado pela redução dos arquivos de dados do banco de dados.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Consultar um pool elástico para obter informações de espaço de armazenamento

As consultas a seguir podem ser usadas para determinar as quantidades de espaço de armazenamento para um pool elástico.  

### <a name="elastic-pool-data-space-used"></a>Espaço de dados do pool elástico usado

Modifique a consulta a seguir para retornar a quantidade de espaço de dados do pool elástico usado.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados do pool elástico alocado e espaço alocado não utilizado

Modifique os exemplos a seguir para retornar uma tabela que lista o espaço alocado e o espaço alocado não utilizado para cada banco de dados em um pool elástico. A tabela ordena os bancos de dados desses bancos de dados com a maior quantidade de espaço alocado não utilizado para a menor quantidade de espaço alocado não utilizado.  As unidades do resultado da consulta estão em MB.  

Os resultados da consulta para determinar o espaço alocado para cada banco de dados no pool podem ser adicionados juntos para determinar o espaço total alocado para o pool elástico. O espaço do pool elástico alocado não deve exceder o tamanho máximo do pool elástico.  

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

O script do PowerShell requer SQL Server PowerShell módulo – consulte [baixar o módulo do PowerShell](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) para instalar.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

A captura de tela a seguir é um exemplo da saída do script:

![exemplo de espaço alocado do pool elástico e espaço alocado não utilizado](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Tamanho máximo de dados do pool elástico

Modifique a seguinte consulta T-SQL para retornar o tamanho máximo de dados do pool elástico.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Recuperar espaço alocado não utilizado

> [!NOTE]
> Esse comando pode afetar o desempenho do banco de dados enquanto está em execução e, se possível, deve ser executado durante períodos de pouco uso.

### <a name="dbcc-shrink"></a>DBCC Shrink

Depois que os bancos de dados tiverem sido identificados para recuperar espaço alocado não utilizado, modifique o nome do banco de dados no comando a seguir para reduzir os arquivos de dado de cada um.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Esse comando pode afetar o desempenho do banco de dados enquanto está em execução e, se possível, deve ser executado durante períodos de pouco uso.  

Para obter mais informações sobre esse comando, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Redução automática

Como alternativa, a redução automática pode ser habilitada para um banco de dados.  A redução automática reduz a complexidade do gerenciamento de arquivos e tem menos impacto no desempenho do banco de dados do que `SHRINKDATABASE` ou `SHRINKFILE`.  A redução automática pode ser particularmente útil para gerenciar pools elásticos com muitos bancos de dados.  No entanto, a redução automática pode ser menos eficaz na recuperação do espaço de arquivo do que `SHRINKDATABASE` e `SHRINKFILE`.
Para habilitar a redução automática, modifique o nome do banco de dados no comando a seguir.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Para obter mais informações sobre esse comando, consulte [Database Set](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) Options.

### <a name="rebuild-indexes"></a>Recompilar índices

Depois que os arquivos de dados do banco são reduzidos, os índices podem se tornar fragmentados e perder a eficácia da otimização do desempenho. Se a degradação do desempenho ocorrer, considere a recriação de índices de banco de dados. Para obter mais informações sobre fragmentação e recriação de índices, consulte [reorganizar e recompilar índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre tamanhos máximos de banco de dados, consulte:
  - [Limites do modelo de compra baseado em vCore do banco de dados SQL do Azure para um banco de dados individual](sql-database-vcore-resource-limits-single-databases.md)
  - [Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-single-databases.md)
  - [Limites do modelo de compra baseado em vCore do banco de dados SQL do Azure para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Recursos limites para pools elásticos usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para obter mais informações sobre o comando `SHRINKDATABASE`, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Para obter mais informações sobre fragmentação e recriação de índices, consulte [reorganizar e recompilar índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

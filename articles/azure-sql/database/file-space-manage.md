---
title: Gestão do espaço de ficheiros Azure SQL Database
description: Esta página descreve como gerir o espaço de ficheiros com bases de dados únicas e agralizadas na Base de Dados Azure SQL, e fornece amostras de código para determinar se precisa de encolher uma única ou uma base de dados agrizada, bem como como realizar uma operação de redução de base de dados.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 3a46e47d6e12d52113bf63342c84a58ca98743d0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789612"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Gerir o espaço de ficheiros para bases de dados na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve diferentes tipos de espaço de armazenamento para bases de dados na Base de Dados Azure SQL, e passos que podem ser dados quando o espaço de ficheiro atribuído precisa ser gerido explicitamente.

> [!NOTE]
> Este artigo não se aplica a Azure SQL Managed Instance.

## <a name="overview"></a>Descrição geral

Com a Base de Dados Azure SQL, existem padrões de carga de trabalho em que a atribuição de ficheiros de dados subjacentes para bases de dados pode tornar-se maior do que a quantidade de páginas de dados usadas. Esta condição pode ocorrer se o espaço utilizado aumentar e se os dados forem eliminados subsequentemente. A razão é porque o espaço de ficheiro atribuído não é automaticamente recuperado quando os dados são eliminados.

Nos cenários abaixo, monitorizar a utilização do espaço de ficheiros e encolher os ficheiros de dados poderá ser necessário:

- Permita o crescimento dos dados num conjunto elástico quando o espaço de ficheiros alocado às respetivas bases de dados atingir o tamanho máximo do conjunto.
- Permita a diminuição do tamanho máximo de uma base de dados individual ou de um conjunto elástico.
- Permita a alteração de uma base de dados individual ou de um conjunto elástico para outro escalão de serviço ou escalão de desempenho com um tamanho máximo mais baixo.

### <a name="monitoring-file-space-usage"></a>Monitorização do uso do espaço do ficheiro

A maioria das métricas de espaço de armazenamento apresentadas no portal Azure e as seguintes APIs medem apenas o tamanho das páginas de dados usadas:

- Métricas baseadas em recursos Azure APIs incluindo powerShell [get-metrics](/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

No entanto, as seguintes APIs também medem o tamanho do espaço atribuído para bases de dados e piscinas elásticas:

- T-SQL:  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Redução de ficheiros de dados

A Azure SQL Database não reduz automaticamente os ficheiros de dados para recuperar o espaço não uustado devido ao impacto potencial no desempenho da base de dados.  No entanto, os clientes podem reduzir os ficheiros de dados através do self-service no momento da sua escolha, seguindo os passos descritos na [recuperação do espaço atribuído não-alocado](#reclaim-unused-allocated-space).

> [!NOTE]
> Ao contrário dos ficheiros de dados, o Azure SQL Database encolhe automaticamente os ficheiros de registo, uma vez que essa operação não tem impacto no desempenho da base de dados.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Compreender tipos de espaço de armazenamento para uma base de dados

Compreender as seguintes quantidades de espaço de armazenamento é importante para gerir o espaço de ficheiros de uma base de dados.

|Quantidade de base de dados|Definição|Comentários|
|---|---|---|
|**Espaço de dados utilizado**|A quantidade de espaço usado para armazenar dados de base de dados em 8 páginas KB.|Geralmente, o espaço utilizado aumenta (diminui) nos inserções (elimina). Em alguns casos, o espaço utilizado não muda em inserções ou elimina dependendo da quantidade e padrão dos dados envolvidos na operação e de qualquer fragmentação. Por exemplo, eliminar uma linha de cada página de dados não diminui necessariamente o espaço utilizado.|
|**Espaço de dados atribuído**|A quantidade de espaço de ficheiros formatado disponibilizado para armazenar dados de base de dados.|A quantidade de espaço atribuído cresce automaticamente, mas nunca diminui após a eliminação. Este comportamento garante que as inserções futuras são mais rápidas, uma vez que o espaço não precisa de ser reformatado.|
|**Espaço de dados atribuído mas não uso**|A diferença entre a quantidade de espaço de dados atribuído e o espaço de dados utilizado.|Esta quantidade representa a quantidade máxima de espaço livre que pode ser recuperada diminuindo os ficheiros de dados da base de dados.|
|**Tamanho máximo dos dados**|A quantidade máxima de espaço que pode ser usada para armazenar dados de bases de dados.|A quantidade de espaço de dados atribuído não pode crescer para além do tamanho máximo dos dados.|

O diagrama seguinte ilustra a relação entre os diferentes tipos de espaço de armazenamento para uma base de dados.

![tipos e relacionamentos de espaço de armazenamento](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Consultar uma única base de dados para informações sobre o espaço de armazenamento

As seguintes consultas podem ser utilizadas para determinar as quantidades de espaço de armazenamento para uma única base de dados.  

### <a name="database-data-space-used"></a>Espaço de dados de dados de base de dados utilizado

Modifique a seguinte consulta para devolver a quantidade de espaço de dados de base de dados utilizado.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados de dados de base de dados atribuído e espaço atribuído não-alocado

Utilize a seguinte consulta para devolver a quantidade de espaço de dados de base de dados atribuído e a quantidade de espaço não utilizado atribuído.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Tamanho máximo dos dados da base de dados

Modifique a seguinte consulta para devolver o tamanho máximo dos dados da base de dados.  As unidades do resultado da consulta estão em bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Compreender tipos de espaço de armazenamento para uma piscina elástica

Compreender as seguintes quantidades de espaço de armazenamento é importante para gerir o espaço de arquivo de uma piscina elástica.

|Quantidade de piscina elástica|Definição|Comentários|
|---|---|---|
|**Espaço de dados utilizado**|A soma do espaço de dados utilizado por todas as bases de dados na piscina elástica.||
|**Espaço de dados atribuído**|A soma do espaço de dados atribuído por todas as bases de dados no pool elástico.||
|**Espaço de dados atribuído mas não uso**|A diferença entre a quantidade de espaço de dados atribuído e o espaço de dados utilizado por todas as bases de dados no pool elástico.|Esta quantidade representa a quantidade máxima de espaço atribuído ao pool elástico que pode ser recuperado através da redução dos ficheiros de dados da base de dados.|
|**Tamanho máximo dos dados**|A quantidade máxima de espaço de dados que pode ser usado pela piscina elástica para todas as suas bases de dados.|O espaço atribuído à piscina elástica não deve exceder o tamanho máximo da piscina elástica.  Se esta condição ocorrer, então o espaço atribuído que não é reutilizado pode ser recuperado diminuindo os ficheiros de dados da base de dados.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Consultar uma piscina elástica para informações de espaço de armazenamento

Pode utilizar as seguintes consultas para determinar as quantidades de espaço de armazenamento de um conjunto elástico.  

### <a name="elastic-pool-data-space-used"></a>Espaço elástico de dados da piscina usado

Modifique a seguinte consulta para devolver a quantidade de espaço elástico de dados do pool utilizado.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Espaço elástico de dados da piscina atribuído e espaço atribuído não-usedido

Modifique os seguintes exemplos para devolver uma tabela que enumera o espaço atribuído e espaço não-alocado para cada base de dados numa piscina elástica. A tabela encomenda bases de dados a partir dessas bases de dados com a maior quantidade de espaço não-alocado até à menor quantidade de espaço não atribuído.  As unidades do resultado da consulta estão em MB.  

Os resultados da consulta para determinar o espaço atribuído a cada base de dados na piscina podem ser adicionados em conjunto para determinar o espaço total atribuído à piscina elástica. O espaço elástico da piscina atribuído não deve exceder o tamanho máximo da piscina elástica.  

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020. Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

O script PowerShell requer módulo POWERShell do servidor SQL – ver [módulo Download PowerShell](/sql/powershell/download-sql-server-ps-module) para instalar.

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

A imagem a seguir é um exemplo da saída do script:

![espaço atribuído em piscina elástica e exemplo de espaço não alocado](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Tamanho máximo de dados de piscina elástico

Modifique a seguinte consulta T-SQL para devolver o tamanho máximo dos dados do pool elástico.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Recuperar o espaço não-alocado

> [!NOTE]
> Este comando pode ter impacto no desempenho da base de dados durante o seu funcionamento e, se possível, deve ser executado durante períodos de baixa utilização.

### <a name="dbcc-shrink"></a>Psiquiatra DBCC

Uma vez identificadas bases de dados para recuperar o espaço atribuído não é percebido, modifique o nome da base de dados no seguinte comando para reduzir os ficheiros de dados de cada base de dados.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Este comando pode ter impacto no desempenho da base de dados durante o seu funcionamento e, se possível, deve ser executado durante períodos de baixa utilização.  

Para obter mais informações sobre este comando, consulte [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Redução automática

Em alternativa, o auto-psiquiatra pode ser ativado para uma base de dados.  A redução automática reduz a complexidade da gestão de ficheiros e é menos impactante para o desempenho da base de dados do que `SHRINKDATABASE` `SHRINKFILE` ou .  O auto-psiquiatra pode ser particularmente útil para gerir piscinas elásticas com muitas bases de dados.  No entanto, a redução automática pode ser menos eficaz na recuperação do espaço de ficheiros do que `SHRINKDATABASE` `SHRINKFILE` e .
Para ativar a redução automática, modifique o nome da base de dados no seguinte comando.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Para obter mais informações sobre este comando, consulte as opções [BASE DE BASE DE BASE.](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

### <a name="rebuild-indexes"></a>Reconstruir índices

Após os ficheiros de dados da base de dados serem encolhidos, os índices podem fragmentar-se e perder a sua eficácia de otimização de desempenho. Se ocorrer uma degradação do desempenho, considere a reconstrução dos índices de base de dados. Para obter mais informações sobre índices de fragmentação e reconstrução, consulte [Reorganizar e Reconstruir Índices](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre os tamanhos máximos da base de dados, consulte:
  - [Limites de modelo de compra baseados em Azure SQL Para uma única base de dados](resource-limits-vcore-single-databases.md)
  - [Limites de recursos das bases de dados individuais com o modelo de compra baseado em DTU](resource-limits-dtu-single-databases.md)
  - [Azure SQL Database vCore-based modelo de compra limites para piscinas elásticas](resource-limits-vcore-elastic-pools.md)
  - [Limites de recursos para piscinas elásticas utilizando o modelo de compra baseado em DTU](resource-limits-dtu-elastic-pools.md)
- Para obter mais informações sobre o `SHRINKDATABASE` comando, consulte [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Para obter mais informações sobre índices de fragmentação e reconstrução, consulte [Reorganizar e Reconstruir Índices](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
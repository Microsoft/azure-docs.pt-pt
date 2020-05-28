---
title: Gestão do espaço de arquivo azure SQL
description: Esta página descreve como gerir o espaço de ficheiros com bases de dados únicas e agrofadas na Base de Dados Azure SQL, e fornece amostras de código para determinar se precisa de encolher uma única ou uma base de dados agrofada, bem como como executar uma operação de redução de base de dados.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: f7c182fc8cd0c9ec74a35396e3cd042ce86d8ee4
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051977"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Gerir espaço de ficheiros para bases de dados na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve diferentes tipos de espaço de armazenamento para bases de dados em Base de Dados Azure SQL, e passos que podem ser dados quando o espaço de ficheiros atribuído precisa de ser explicitamente gerido.

> [!NOTE]
> Este artigo não se aplica à Instância Gerida Azure SQL.

## <a name="overview"></a>Descrição geral

Com a Base de Dados Azure SQL, existem padrões de carga de trabalho onde a atribuição de ficheiros de dados subjacentes para bases de dados pode tornar-se maior do que a quantidade de páginas de dados usadas. Esta condição pode ocorrer se o espaço utilizado aumentar e se os dados forem eliminados subsequentemente. A razão é porque o espaço de ficheiro atribuído não é automaticamente recuperado quando os dados são eliminados.

Nos cenários abaixo, monitorizar a utilização do espaço de ficheiros e encolher os ficheiros de dados poderá ser necessário:

- Permita o crescimento dos dados num conjunto elástico quando o espaço de ficheiros alocado às respetivas bases de dados atingir o tamanho máximo do conjunto.
- Permita a diminuição do tamanho máximo de uma base de dados individual ou de um conjunto elástico.
- Permita a alteração de uma base de dados individual ou de um conjunto elástico para outro escalão de serviço ou escalão de desempenho com um tamanho máximo mais baixo.

### <a name="monitoring-file-space-usage"></a>Monitorização do uso do espaço de ficheiros

A maioria das métricas do espaço de armazenamento apresentadas no portal Azure e as seguintes APIs apenas medem o tamanho das páginas de dados usadas:

- Métricas baseadas em Recursos Azure, incluindo [métricas de get-metrics](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric) PowerShell
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

No entanto, as seguintes APIs também medem a dimensão do espaço atribuído às bases de dados e aos pools elásticos:

- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Redução dos ficheiros de dados

A Base de Dados Azure SQL não encolhe automaticamente os ficheiros de dados para recuperar o espaço atribuído não utilizado devido ao impacto potencial no desempenho da base de dados.  No entanto, os clientes podem reduzir os ficheiros de dados através do self-service no momento da sua escolha, seguindo os passos descritos na [recuperação](#reclaim-unused-allocated-space)do espaço atribuído não utilizado .

> [!NOTE]
> Ao contrário dos ficheiros de dados, a Base de Dados Azure SQL encolhe automaticamente os ficheiros de registo, uma vez que essa operação não afeta o desempenho da base de dados.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Compreender tipos de espaço de armazenamento para uma base de dados

Compreender as seguintes quantidades de espaço de armazenamento são importantes para gerir o espaço de arquivo de uma base de dados.

|Quantidade de base de dados|Definição|Comentários|
|---|---|---|
|**Espaço de dados utilizado**|A quantidade de espaço usado para armazenar dados de base de dados em 8 páginas KB.|Geralmente, o espaço utilizado aumenta (diminui) nas inserções (elimina). Em alguns casos, o espaço utilizado não muda em inserções ou elimina dependendo da quantidade e padrão dos dados envolvidos na operação e de qualquer fragmentação. Por exemplo, apagar uma linha de cada página de dados não diminui necessariamente o espaço utilizado.|
|**Espaço de dados atribuído**|A quantidade de espaço de ficheiro sintetizada disponibilizado para armazenar dados da base de dados.|A quantidade de espaço atribuído cresce automaticamente, mas nunca diminui após a eliminação. Este comportamento garante que as futuras inserções são mais rápidas, uma vez que o espaço não precisa de ser reformado.|
|**Espaço de dados alocado, mas não utilizado**|A diferença entre a quantidade de espaço de dados atribuído e o espaço de dados utilizado.|Esta quantidade representa a quantidade máxima de espaço livre que pode ser recuperada através da redução dos ficheiros de dados da base de dados.|
|**Tamanho máximo de dados**|A quantidade máxima de espaço que pode ser usada para armazenar dados de base de dados.|A quantidade de espaço de dados atribuído não pode crescer para além do tamanho máximo de dados.|

O diagrama que se segue ilustra a relação entre os diferentes tipos de espaço de armazenamento para uma base de dados.

![tipos de espaço de armazenamento e relacionamentos](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Consulta de uma única base de dados para informações do espaço de armazenamento

As seguintes consultas podem ser utilizadas para determinar as quantidades espaciais de armazenamento para uma única base de dados.  

### <a name="database-data-space-used"></a>Espaço de dados de base de dados utilizado

Modifique a seguinte consulta para devolver a quantidade de espaço de dados de base de dados utilizado.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados de base de dados atribuído e espaço atribuído não utilizado

Utilize a seguinte consulta para devolver a quantidade de espaço de dados atribuído e a quantidade de espaço não utilizado atribuído.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Tamanho máximo de dados de dados de base de dados

Modifique a seguinte consulta para devolver o tamanho máximo dos dados da base de dados.  As unidades do resultado da consulta estão em bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Compreender tipos de espaço de armazenamento para uma piscina elástica

Compreender as seguintes quantidades de espaço de armazenamento são importantes para gerir o espaço de arquivo de uma piscina elástica.

|Quantidade elástica da piscina|Definição|Comentários|
|---|---|---|
|**Espaço de dados utilizado**|A soma do espaço de dados utilizado por todas as bases de dados da piscina elástica.||
|**Espaço de dados atribuído**|A soma do espaço de dados atribuído por todas as bases de dados do conjunto elástico.||
|**Espaço de dados alocado, mas não utilizado**|A diferença entre a quantidade de espaço de dados atribuído e o espaço de dados utilizado por todas as bases de dados do pool elástico.|Esta quantidade representa a quantidade máxima de espaço atribuído ao conjunto elástico que pode ser recuperado através da redução dos ficheiros de dados da base de dados.|
|**Tamanho máximo de dados**|A quantidade máxima de espaço de dados que pode ser utilizado pelo conjunto elástico para todas as suas bases de dados.|O espaço atribuído à piscina elástica não deve exceder o tamanho máximo da piscina elástica.  Se esta condição ocorrer, então o espaço atribuído que não é utilizado pode ser recuperado através da redução dos ficheiros de dados da base de dados.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Consulta de uma piscina elástica para informações do espaço de armazenamento

As seguintes consultas podem ser usadas para determinar as quantidades espaciais de armazenamento para uma piscina elástica.  

### <a name="elastic-pool-data-space-used"></a>Espaço de dados de piscina elástica usado

Modifique a seguinte consulta para devolver a quantidade de espaço de dados elástico utilizado.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados de piscina elástica atribuído e espaço atribuído não utilizado

Modifique os seguintes exemplos para devolver uma tabela com o espaço atribuído e o espaço atribuído não utilizado para cada base de dados numa piscina elástica. A tabela encomenda bases de dados dessas bases de dados com a maior quantidade de espaço atribuído não utilizado para a menor quantidade de espaço atribuído não utilizado.  As unidades do resultado da consulta estão em MB.  

Os resultados da consulta para determinar o espaço atribuído para cada base de dados da piscina podem ser adicionados em conjunto para determinar o espaço total atribuído para a piscina elástica. O espaço elástico da piscina atribuído não deve exceder o tamanho máximo da piscina elástica.  

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

O script PowerShell requer módulo PowerShell do Servidor SQL – consulte [o módulo Descarregamento PowerShell](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) para instalar.

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

A seguinte imagem é um exemplo da saída do script:

![piscina elástica espaço atribuído e espaço não utilizado alocada exemplo](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Dados da piscina elástica tamanho máximo

Modifique a seguinte consulta T-SQL para devolver o tamanho máximo dos dados da piscina elástica.  As unidades do resultado da consulta estão em MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Recuperar espaço atribuído não utilizado

> [!NOTE]
> Este comando pode impactar o desempenho da base de dados durante o seu funcionamento, e se possível deve ser executado durante períodos de baixa utilização.

### <a name="dbcc-shrink"></a>Psiquiatra dBCC

Uma vez identificadas bases de dados para recuperar espaço atribuído não utilizado, modifique o nome da base de dados no seguinte comando para reduzir os ficheiros de dados de cada base de dados.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Este comando pode impactar o desempenho da base de dados durante o seu funcionamento, e se possível deve ser executado durante períodos de baixa utilização.  

Para mais informações sobre este comando, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Auto-psiquiatra

Em alternativa, o encolhimento automático pode ser ativado para uma base de dados.  O encolher de automóveis reduz a complexidade da gestão de ficheiros e é menos impactante para o desempenho da base de dados do que `SHRINKDATABASE` ou `SHRINKFILE` .  O encolhimento automático pode ser particularmente útil para a gestão de piscinas elásticas com muitas bases de dados.  No entanto, o encolhimento automático pode ser menos eficaz na recuperação do espaço dos ficheiros do que `SHRINKDATABASE` `SHRINKFILE` .
Para ativar o encolhimento automático, modifique o nome da base de dados no seguinte comando.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Para mais informações sobre este comando, consulte as opções [de DEFINIÇÃO DE BASE DE DADOS.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

### <a name="rebuild-indexes"></a>Índices de reconstrução

Após a recolha de ficheiros de dados de bases de dados, os índices podem fragmentar-se e perder a sua eficácia de otimização de desempenho. Se ocorrer uma degradação do desempenho, considere a reconstrução dos índices de base de dados. Para obter mais informações sobre índices de fragmentação e reconstrução, consulte [Reorganizar e Reconstruir Índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre tamanhos máximos de base de dados, consulte:
  - [Limites do modelo de compra baseado em Azure SQL base de dados vCore para uma única base de dados](resource-limits-vcore-single-databases.md)
  - [Limites de recursos para bases de dados únicas utilizando o modelo de compra baseado em DTU](resource-limits-dtu-single-databases.md)
  - [Azure SQL Base de Dados vCore limites de modelos de compra baseados em piscinas elásticas](resource-limits-vcore-elastic-pools.md)
  - [Limites de recursos para piscinas elásticas utilizando o modelo de compra baseado em DTU](resource-limits-dtu-elastic-pools.md)
- Para mais informações sobre o `SHRINKDATABASE` comando, consulte [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Para obter mais informações sobre índices de fragmentação e reconstrução, consulte [Reorganizar e Reconstruir Índices](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

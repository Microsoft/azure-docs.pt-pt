---
title: Criar e gerir conjuntos elásticos - base de dados SQL do Azure | Documentos da Microsoft
description: Criar e gerir conjuntos elásticos SQL do Azure.
services: sql-database
ms.service: sql-database
subservice: elastic-pool
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: 0c939956a8f3336b5071748a8c2bdf8840b749ad
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466074"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Criar e gerir conjuntos elásticos na base de dados do Azure SQL

Com um conjunto elástico, determine a quantidade de recursos que o conjunto elástico necessita para processar a carga de trabalho de seus bancos de dados e a quantidade de recursos para cada base de dados em pool.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Portal do Azure: Gerir conjuntos elásticos e bases de dados agrupadas

Todas as definições de agrupamento podem ser encontradas num único lugar: os **configurar conjunto** painel. Para obter aqui, encontrar um conjunto elástico no portal e clique em **configurar conjunto** da parte superior do painel ou no menu de recursos no lado esquerdo.

Aqui pode fazer qualquer combinação das seguintes alterações e guardá-los todos num lote:

1. Alterar o escalão de serviço do conjunto
2. Aumentar o desempenho (DTUS ou vCores) e o armazenamento ou reduzir verticalmente
3. Adicionar ou remover bases de dados de/para o conjunto
4. Definir um min (garantida) e máximo de limite de desempenho para os conjuntos de bases de dados
5. Reveja o resumo de custos para ver todas as alterações à sua fatura como resultado das suas seleções de novo

![Painel de configuração do conjunto elástico](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Gerir conjuntos elásticos e bases de dados agrupadas

Para criar e gerir conjuntos elásticos da base de dados SQL e bases de dados agrupadas com o Azure PowerShell, utilize os seguintes cmdlets do PowerShell. Se precisar de instalar ou atualizar o PowerShell, veja [módulo de instalar o Azure PowerShell](/powershell/azure/install-azurerm-ps). Para criar e gerir os servidores lógicos de um conjunto elástico, consulte [servidores lógicos, criar e gerida](sql-database-logical-servers.md). Para criar e gerir regras de firewall, consulte [criar e gerir regras de firewall com o PowerShell](sql-database-firewall-configure.md#manage-firewall-rules-using-azure-powershell).

> [!TIP]
> Para scripts de exemplo do PowerShell, consulte [criar conjuntos elásticos e mover bases de dados entre conjuntos e para fora de um conjunto com o PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) e [utilize o PowerShell para monitorizar e dimensionar um conjunto de elástico de SQL na base de dados do Azure SQL](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Cria um conjunto de bases de dados elásticas num servidor SQL lógico.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Obtém conjuntos elásticos e os respetivos valores de propriedade num servidor SQL lógico.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modifica as propriedades de um conjunto de bases de dados elásticas num servidor SQL lógico. Por exemplo, utilizar o **StorageMB** propriedade para modificar o armazenamento máximo de um conjunto elástico.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Elimina um conjunto de bases de dados elásticas num servidor SQL lógico.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Obtém o estado de operações num conjunto elástico num servidor SQL lógico.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Cria uma nova base de dados num conjunto existente ou como uma base de dados. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtém uma ou mais bases de dados.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Define as propriedades para uma base de dados ou move uma base de dados existente para, de ou entre conjuntos elásticos.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Remove uma base de dados.|

> [!TIP]
> Criação de muitas bases de dados num conjunto elástico pode demorar tempo quando terminado a utilizar o portal ou cmdlets do PowerShell que criam apenas uma base de dados cada vez. Para automatizar a criação de um conjunto elástico, veja [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>CLI do Azure: Gerir conjuntos elásticos e bases de dados agrupadas

Para criar e gerir conjuntos elásticos da base de dados SQL com o [CLI do Azure](/cli/azure), utilize o seguinte procedimento [base de dados de SQL do Azure CLI](/cli/azure/sql/db) comandos. Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para scripts de exemplo da CLI do Azure, consulte [CLI de utilização para mover uma base de dados SQL do Azure num conjunto elástico](scripts/sql-database-move-database-between-pools-cli.md) e [utilização do Azure CLI para dimensionar um conjunto elástico de SQL na base de dados do Azure SQL](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Cria um conjunto elástico.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Devolve uma lista de conjuntos elásticos num servidor.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Devolve uma lista de bases de dados num conjunto elástico.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Também inclui definições de DTU do conjunto disponível, limites de armazenamento e por definições de base de dados. Para reduzir a verbosidade, limites de armazenamento adicional e por base de dados, as definições são ocultadas por predefinição.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Atualiza um conjunto elástico.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Elimina o conjunto elástico.|

## <a name="transact-sql-manage-pooled-databases"></a>O Transact-SQL: Gerir bases de dados agrupadas

Para criar e mover bases de dados dentro de conjuntos elásticos existentes ou para devolver informações sobre um conjunto elástico da base de dados SQL com o Transact-SQL, utilize os seguintes comandos do T-SQL. Pode emitir estes comandos no portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), ou qualquer outro programa que pode ligar a um servidor de base de dados do Azure SQL e passar o Transact-SQL comandos. Para criar e gerir regras de firewall com o T-SQL, consulte [gerir regras de firewall com o Transact-SQL](sql-database-firewall-configure.md#manage-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> Não é possível criar, atualizar ou eliminar um conjunto elástico de SQL Database do Azure com o Transact-SQL. Pode adicionar ou remover bases de dados de um conjunto elástico, e pode usar DMVs para devolver informações sobre conjuntos elásticos existentes.
>

| Comando | Descrição |
| --- | --- |
|[Criar base de dados (banco de dados SQL do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria uma nova base de dados num conjunto existente ou como uma base de dados. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [Alterar base de dados (banco de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Mova uma base de dados para, de ou entre conjuntos elásticos.|
|[REMOVER a base de dados (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Elimina uma base de dados.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Devolve estatísticas de utilização de recursos para todos os conjuntos de bases de dados elásticas num servidor lógico. Para cada conjunto de bases de dados elásticas, há uma linha para cada segundo 15 relatórios janela (quatro linhas por minuto). Isto inclui a utilização da CPU, e/s, registo, o consumo de armazenamento e pedido/sessões em simultâneo por todas as bases de dados no conjunto.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devolve o edition (camada de serviço), o objetivo de serviço (escalão de preço) e o nome do conjunto elástico, se existir, para uma base de dados SQL do Azure ou um Azure SQL Data Warehouse. Se a sessão iniciada no banco de dados mestra num servidor de base de dados do Azure SQL, retorna informações sobre todas as bases de dados. Para o Azure SQL Data Warehouse, tem de estar ligado à base de dados mestra.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: Gerir conjuntos elásticos e bases de dados agrupadas

Para criar e gerir conjuntos elásticos da base de dados SQL e bases de dados agrupadas, utilize estes pedidos de REST API.

| Comando | Descrição |
| --- | --- |
|[Conjuntos elásticos - criar ou atualizar](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_createorupdate)|Cria um novo conjunto elástico ou atualiza um conjunto elástico existente.|
|[Conjuntos elásticos - Delete](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_delete)|Elimina o conjunto elástico.|
|[Conjuntos elásticos - Get](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_get)|Obtém um conjunto elástico.|
|[Conjuntos elásticos - lista por servidor](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_listbyserver)|Devolve uma lista de conjuntos elásticos num servidor.|
|[Conjuntos elásticos - atualização](https://docs.microsoft.com/rest/api/sql/elasticpools/elasticpools_listbyserver)|Atualiza um conjunto elástico existente.|
|[Atividades de conjunto elástico](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Devolve as atividades de conjunto elástico.|
|[Conjunto elástico da base de dados de atividades](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Devolve a atividade nas bases de dados dentro de um conjunto elástico.|
|[Bases de dados - criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Cria uma nova base de dados ou atualiza a base de dados existente.|
|[Bases de dados - Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Obtém uma base de dados.|
|[Bases de dados - lista por conjunto elástico](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Devolve uma lista de bases de dados num conjunto elástico.|
|[Bases de dados - lista por servidor](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Devolve uma lista de bases de dados num servidor.|
|[Bases de dados - atualização](https://docs.microsoft.com/rest/api/sql/databases/update)|Atualiza a base de dados existente.|

## <a name="next-steps"></a>Passos Seguintes

* Para ver um vídeo, consulte [curso de vídeo do Microsoft Virtual Academy sobre capacidades elásticas da base de dados do Azure SQL](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Para obter um tutorial de SaaS que utilizam conjuntos elásticos, veja [introdução à aplicação Wingtip SaaS](sql-database-wtp-overview.md).

---
title: Gerir conjuntos elásticos
description: Crie e gere as piscinas elásticas Azure SQL Database utilizando o portal Azure, PowerShell, o Azure CLI, Transact-SQL (T-SQL) e Rest API.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: bd3917c42859d4240fbb3a75f951ff38a548e204
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330813"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Gerir piscinas elásticas em Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Com uma piscina elástica, você determina a quantidade de recursos que a piscina elástica requer para lidar com a carga de trabalho das suas bases de dados, e a quantidade de recursos para cada base de dados agrária.

## <a name="azure-portal"></a>Portal do Azure

Todas as configurações da piscina podem ser encontradas num só local: a lâmina da **piscina Configure.** Para chegar aqui, encontre uma piscina elástica no portal Azure e clique na **piscina Configure,** quer a partir da parte superior da lâmina, quer do menu de recursos à esquerda.

A partir daqui pode fazer qualquer combinação das seguintes alterações e guardá-las todas num só lote:

1. Alterar o nível de serviço da piscina
2. Dimensione o desempenho (DTU ou vCores) e o armazenamento para cima ou para baixo
3. Adicionar ou remover bases de dados de/para a piscina
4. Desaver um min (garantido) e limite máximo de desempenho para as bases de dados nas piscinas
5. Reveja o resumo de custos para ver quaisquer alterações à sua conta como resultado das suas novas seleções

![Lâmina de configuração de piscina elástica](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerir piscinas elásticas SQL Database e bases de dados em conjunto com a Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se precisar de instalar ou atualizar o PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). Para criar e gerir os servidores para uma piscina elástica, consulte [Criar e gerir servidores](logical-servers.md). Para criar e gerir as regras de firewall, consulte [criar e gerir as regras de firewall utilizando o PowerShell](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Para scripts de exemplo PowerShell, consulte [Criar piscinas elásticas e mover bases de dados entre piscinas e para fora de uma piscina utilizando PowerShell](scripts/move-database-between-elastic-pools-powershell.md) e [Use PowerShell para monitorizar e escalar uma piscina elástica SQL na Base de Dados Azure SQL](scripts/monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Cria uma piscina elástica.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Obtém piscinas elásticas e seus valores de propriedade.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Modifica propriedades de uma piscina elástica Por exemplo, utilize a propriedade **StorageMB** para modificar o armazenamento máximo de uma piscina elástica.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Elimina uma piscina elástica.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Obtém o estado das operações numa piscina elástica|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Cria uma nova base de dados num pool existente ou como uma única base de dados. |
|[Base de Dados Get-AzSql](/powershell/module/az.sql/get-azsqldatabase)|Obtém uma ou mais bases de dados.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Define propriedades para uma base de dados, ou move uma base de dados existente para dentro, para fora ou entre piscinas elásticas.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Remove uma base de dados.|

> [!TIP]
> A criação de muitas bases de dados numa piscina elástica pode levar tempo quando feito usando o portal ou os cmdlets PowerShell que criam apenas uma única base de dados de cada vez. Para automatizar a criação numa piscina elástica, consulte [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>CLI do Azure

Para criar e gerir piscinas elásticas SQL Database com o [Azure CLI,](/cli/azure)utilize os seguintes comandos [Azure CLI SQL Database.](/cli/azure/sql/db) Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para scripts de exemplo Azure CLI, consulte [use CLI para mover uma base de dados na Base de Dados SQL numa piscina elástica SQL](scripts/move-database-between-elastic-pools-cli.md) e [use Azure CLI para escalar uma piscina elástica SQL na Base de Dados Azure SQL](scripts/scale-pool-cli.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[az sql piscina elástica criar](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Cria uma piscina elástica.|
|[az sql lista de piscina elástica](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Devolve uma lista de piscinas elásticas num servidor.|
|[az sql elástico-pool lista-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Devolve uma lista de bases de dados numa piscina elástica.|
|[az sql elastic-pool lista-edições](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Também inclui configurações DTU de piscina disponíveis, limites de armazenamento e por definições de base de dados. Para reduzir a verbosidade, os limites de armazenamento adicionais e as definições por base de dados são ocultados por padrão.|
|[az sql elastic-pool atualização](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Atualiza uma piscina elástica.|
|[az sql elástico-piscina delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Elimina a piscina elástica.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Para criar e mover bases de dados dentro de piscinas elásticas existentes ou para devolver informações sobre uma piscina elástica SQL Database com Transact-SQL, utilize os seguintes comandos T-SQL. Pode emitir estes comandos utilizando o portal Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code,](https://code.visualstudio.com/docs)ou qualquer outro programa que possa ligar-se a um servidor e passar comandos Transact-SQL. Para criar e gerir as regras de firewall utilizando o T-SQL, consulte as regras de [firewall utilizando a Transact-SQL](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Não é possível criar, atualizar ou eliminar uma piscina elástica Azure SQL Database utilizando o Transact-SQL. Pode adicionar ou remover bases de dados de uma piscina elástica, e pode usar DMVs para devolver informações sobre piscinas elásticas existentes.
>

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE (Base de dados Azure SQL)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria uma nova base de dados num pool existente ou como uma única base de dados. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [ALTER DATABASE (Base de dados Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Mova uma base de dados para dentro, para fora ou entre piscinas elásticas.|
|[BASE DE DADOS DROP (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Elimina uma base de dados.|
|[sys.elastic_pool_resource_stats (Base de Dados Azure SQL)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Devolve estatísticas de utilização de recursos para todas as piscinas elásticas de um servidor. Para cada piscina elástica, há uma linha para cada janela de reporte de 15 segundos (quatro linhas por minuto). Isto inclui CPU, IO, Log, consumo de armazenamento e utilização simultânea de pedido/sessão por todas as bases de dados da piscina.|
|[sys.database_service_objetives (Base de Dados Azure SQL)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devolve a edição (nível de serviço), o objetivo de serviço (nível de preços) e o nome elástico da piscina, caso existam, para uma base de dados na SQL Database ou na Azure Synapse Analytics (anteriormente SQL Data Warehouse). Se iniciado na base de dados principal num servidor, retorna a informação em todas as bases de dados. Para a Azure Synapse Analytics, deve estar ligado à base de dados principal.|

## <a name="rest-api"></a>API REST

Para criar e gerir piscinas elásticas SQL Database e bases de dados em conjunto, utilize estes pedidos de API REST.

| Comando | Descrição |
| --- | --- |
|[Piscinas elásticas - Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Cria uma nova piscina elástica ou atualiza uma piscina elástica existente.|
|[Piscinas elásticas - Eliminar](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Elimina a piscina elástica.|
|[Piscinas elásticas - Obter](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Tem uma piscina elástica.|
|[Piscinas elásticas - Lista por servidor](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Devolve uma lista de piscinas elásticas num servidor.|
|[Piscinas elásticas - Atualização](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Atualiza uma piscina elástica existente.|
|[Atividades de piscina elástica](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Devolve atividades elásticas de piscina.|
|[Atividades de base de dados de piscina elástica](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Devolve a atividade em bases de dados dentro de uma piscina elástica.|
|[Bases de dados - Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Cria uma nova base de dados ou atualiza uma base de dados existente.|
|[Bases de dados - Obter](https://docs.microsoft.com/rest/api/sql/databases/get)|Tem uma base de dados.|
|[Bases de dados - Lista por piscina elástica](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Devolve uma lista de bases de dados numa piscina elástica.|
|[Bases de dados - Lista por servidor](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retorna uma lista de bases de dados num servidor.|
|[Bases de dados - Atualização](https://docs.microsoft.com/rest/api/sql/databases/update)|Atualiza uma base de dados existente.|

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](saas-tenancy-app-design-patterns.md).
* Para um tutorial SaaS utilizando piscinas elásticas, consulte [Introdução à aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md).

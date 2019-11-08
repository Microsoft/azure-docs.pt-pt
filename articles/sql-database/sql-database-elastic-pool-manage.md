---
title: Gerir conjuntos elásticos
description: Crie e gerencie pools elásticos do SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4ae2e84ab5f6108de4e8236c39371cf9658809d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823872"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Gerenciar Pools elásticos no banco de dados SQL do Azure

Com um pool elástico, você determina a quantidade de recursos que o pool elástico requer para lidar com a carga de trabalho de seus bancos de dados e a quantidade de recursos para cada banco de dados em pool.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Portal do Azure: gerenciar pools elásticos e bancos de dados em pool

Todas as configurações de pool podem ser encontradas em um único local: a folha **Configurar pool** . Para obter aqui, localize um pool elástico no portal e clique em **Configurar pool** na parte superior da folha ou no menu de recursos à esquerda.

A partir daqui, você pode fazer qualquer combinação das seguintes alterações e salvá-las em um lote:

1. Alterar a camada de serviço do pool
2. Dimensionar o desempenho (DTU ou vCores) e o armazenamento para cima ou para baixo
3. Adicionar ou Remover bancos de dados de/para o pool
4. Definir um limite de desempenho mínimo (garantido) e máximo para os bancos de dados nos pools
5. Examine o resumo de custo para exibir as alterações feitas na sua fatura como resultado de suas novas seleções

![Folha de configuração do pool elástico](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: gerenciar pools elásticos e bancos de dados em pool

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerenciar pools elásticos do banco de dados SQL e bancos de dados em pool com Azure PowerShell, use os seguintes cmdlets do PowerShell. Se você precisar instalar ou atualizar o PowerShell, consulte [instalar Azure PowerShell Module](/powershell/azure/install-az-ps). Para criar e gerenciar os servidores de banco de dados SQL para um pool elástico, consulte [criar e gerenciar servidores de banco de dados SQL](sql-database-servers.md). Para criar e gerenciar regras de firewall, consulte [criar e gerenciar regras de firewall usando o PowerShell](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Para scripts de exemplo do PowerShell, consulte [criar pools elásticos e mover bancos de dados entre pools e fora de um pool usando o PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) e [usar o PowerShell para monitorar e dimensionar um pool elástico do SQL no banco de dados SQL do Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Cria um pool elástico.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Obtém pools elásticos e seus valores de propriedade.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Modifica as propriedades de um pool elástico, por exemplo, use a propriedade **StorageMB** para modificar o armazenamento máximo de um pool elástico.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Exclui um pool elástico.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Obtém o status das operações em um pool elástico|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Cria um novo banco de dados em um pool existente ou como um banco de dados individual. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Obtém uma ou mais bases de dados.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Define as propriedades de um banco de dados ou move um banco de dados existente para fora ou entre pools elásticos.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Remove um banco de dados.|

> [!TIP]
> A criação de muitos bancos de dados em um pool elástico pode levar tempo ao terminar de usar o portal ou cmdlets do PowerShell que criam apenas um banco de dados de cada vez. Para automatizar a criação em um pool elástico, consulte [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>CLI do Azure: gerenciar pools elásticos e bancos de dados em pool

Para criar e gerenciar pools elásticos do banco de dados SQL com o [CLI do Azure](/cli/azure), use os comandos de [banco de dados sql](/cli/azure/sql/db) a seguir CLI do Azure. Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para CLI do Azure scripts de exemplo, consulte [usar a CLI para mover um banco de dados SQL do Azure em um pool elástico do SQL](scripts/sql-database-move-database-between-pools-cli.md) e [usar CLI do Azure para dimensionar um pool elástico do SQL no banco de dados SQL do Azure](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[AZ SQL elástico-pool Create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Cria um pool elástico.|
|[AZ SQL elástico – lista de pools](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Retorna uma lista de pools elásticos em um servidor.|
|[AZ SQL elástico-pool List-bancos de os](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Retorna uma lista de bancos de dados em um pool elástico.|
|[AZ SQL elástico-List pool-Editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Também inclui configurações de DTU de pool disponíveis, limites de armazenamento e configurações por banco de dados. Para reduzir o detalhamento, limites de armazenamento adicionais e configurações por banco de dados ficam ocultos por padrão.|
|[AZ SQL elástico – atualização de pool](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Atualiza um pool elástico.|
|[AZ SQL elástico-pool Delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Exclui o pool elástico.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: gerenciar bancos de dados em pool

Para criar e mover bancos de dados dentro de pools elásticos existentes ou para retornar informações sobre um pool elástico do banco de dados SQL com o Transact-SQL, use os comandos T-SQL a seguir. Você pode emitir esses comandos usando o portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)ou qualquer outro programa que possa se conectar a um servidor de banco de dados SQL do Azure e passar comandos Transact-SQL. Para criar e gerenciar regras de firewall usando o T-SQL, consulte [gerenciar regras de firewall usando o Transact-SQL](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Você não pode criar, atualizar ou excluir um pool elástico do banco de dados SQL do Azure usando o Transact-SQL. Você pode adicionar ou Remover bancos de dados de um pool elástico e pode usar DMVs para retornar informações sobre os pools elásticos existentes.
>

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE (banco de dados SQL do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria um novo banco de dados em um pool existente ou como um banco de dados individual. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE (banco de dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Mover um banco de dados para dentro, fora ou entre pools elásticos.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Exclui um banco de dados.|
|[sys. elastic_pool_resource_stats (banco de dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Retorna estatísticas de uso de recursos para todos os pools elásticos em um servidor de banco de dados SQL. Para cada pool elástico, há uma linha para cada janela de relatório de 15 segundos (quatro linhas por minuto). Isso inclui CPU, e/s, log, consumo de armazenamento e utilização de solicitação/sessão simultânea por todos os bancos de dados no pool.|
|[sys. database_service_objectives (banco de dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (camada de serviço), o objetivo de serviço (tipo de preço) e o nome do pool elástico, se houver, para um banco de dados SQL do Azure ou um SQL Data Warehouse do Azure. Se estiver conectado ao banco de dados mestre em um servidor de banco de dados SQL do Azure, retorna informações sobre todos os bancos de dados. Para o Azure SQL Data Warehouse, você deve estar conectado ao banco de dados mestre.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>API REST: gerenciar pools elásticos e bancos de dados em pool

Para criar e gerenciar pools elásticos do banco de dados SQL e bancos de dados em pool, use essas solicitações da API REST.

| Comando | Descrição |
| --- | --- |
|[Pools elásticos – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Cria um novo pool elástico ou atualiza um pool elástico existente.|
|[Pools elásticos – excluir](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Exclui o pool elástico.|
|[Pools elásticos – obter](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Obtém um pool elástico.|
|[Pools elásticos – listar por servidor](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Retorna uma lista de pools elásticos em um servidor.|
|[Pools elásticos – atualizar](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Atualiza um pool elástico existente.|
|[Atividades do pool elástico](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Retorna atividades do pool elástico.|
|[Atividades de banco de dados do pool elástico](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Retorna atividade em bancos de dados dentro de um pool elástico.|
|[Bancos de dados – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Cria um novo banco de dados ou atualiza um banco de dados existente.|
|[Bancos de dados-obter](https://docs.microsoft.com/rest/api/sql/databases/get)|Obtém um banco de dados.|
|[Bancos de dados-listar por pool elástico](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Retorna uma lista de bancos de dados em um pool elástico.|
|[Bancos de dados-listar por servidor](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retorna uma lista de bancos de dados em um servidor.|
|[Bancos de dados-atualização](https://docs.microsoft.com/rest/api/sql/databases/update)|Atualiza um banco de dados existente.|

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre os padrões de estrutura de aplicações SaaS que utilizam conjuntos elásticos, consulte o artigo [Padrões de Estrutura de Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Para obter um tutorial de SaaS usando pools elásticos, consulte [introdução ao aplicativo SaaS Wingtip](sql-database-wtp-overview.md).

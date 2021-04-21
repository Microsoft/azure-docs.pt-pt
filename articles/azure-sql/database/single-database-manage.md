---
title: Criar & gerir servidores e bases de dados individuais
description: Saiba como criar e gerir servidores e bases de dados individuais na Base de Dados Azure SQL utilizando o portal Azure, PowerShell, o Azure CLI, Transact-SQL (T-SQL) e Rest-API.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 4aaabdb3d21c41b973b21e6e52442be132796196
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781600"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Criar e gerir servidores e bases de dados individuais na Base de Dados Azure SQL

Pode criar e gerir servidores e bases de dados individuais na Base de Dados Azure SQL utilizando o portal Azure, PowerShell, O Azure CLI, REST API e Transact-SQL.

## <a name="the-azure-portal"></a>O portal do Azure

Pode criar o grupo de recursos para a Base de Dados Azure SQL antes do tempo ou enquanto cria o próprio servidor.

### <a name="create-a-server"></a>Criar um servidor

Para criar um servidor utilizando o [portal Azure,](https://portal.azure.com)crie um novo recurso de [servidor](logical-servers.md) a partir do Azure Marketplace. Em alternativa, pode criar o servidor quando implementar uma Base de Dados Azure SQL.

  ![criar servidor](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Criar uma base de dados em branco ou de amostra

Para criar uma única Base de Dados Azure SQL utilizando o [portal Azure,](https://portal.azure.com)escolha o recurso Azure SQL Database no Azure Marketplace. Pode criar o grupo de recursos e o servidor antes do tempo ou enquanto cria a própria base de dados. Pode criar uma base de dados em branco ou criar uma base de dados de amostras baseada em Adventure Works LT.

  ![criar base de dados-1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Para obter informações sobre a seleção do nível de preços da sua base de dados, consulte [o modelo de compra baseado em DTU](service-tiers-dtu.md) e o modelo de compra baseado em [vCore.](service-tiers-vcore.md)

## <a name="manage-an-existing-server"></a>Gerir um servidor existente

Para gerir um servidor existente, navegue para o servidor utilizando uma série de métodos - como a partir de uma página de base de dados específica, a página de **servidores SQL** ou a página **de todos os recursos.**

Para gerir uma base de dados existente, navegue na página **de bases de dados SQL** e selecione a base de dados que pretende gerir. A imagem que se segue mostra como começar a configurar uma firewall de nível de servidor para uma base de dados a partir da página **'Vista Geral'** para uma base de dados.

   ![regra de firewall do servidor](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Para configurar propriedades de desempenho para uma base de dados, consulte [o modelo de compra baseado em DTU](service-tiers-dtu.md) e o modelo de compra baseado em [vCore.](service-tiers-vcore.md)
> [!TIP]
> Para obter um arranque rápido do portal Azure, consulte [criar uma base de dados na Base de Dados SQL no portal Azure](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerir servidores, bases de dados individuais e aginhadas e firewalls de nível de servidor com Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se precisar de instalar ou atualizar o PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo PowerShell, consulte [Use PowerShell para criar uma base de dados na Base de Dados SQL e configurar uma regra de firewall ao nível do servidor](scripts/create-and-configure-database-powershell.md) e Monitor e escalar uma base de [dados na Base de Dados SQL utilizando powerShell](scripts/monitor-and-scale-database-powershell.md).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Cria uma base de dados |
|[Base de Dados Get-AzSql](/powershell/module/az.sql/get-azsqldatabase)|Obtém uma ou mais bases de dados|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Define propriedades para uma base de dados, ou move uma base de dados existente em uma piscina elástica|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Remove uma base de dados|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Cria um grupo de recursos|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Cria um servidor|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Devolve informações sobre servidores|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Modifica propriedades de um servidor|
|[Remover-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Remove um servidor|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Cria uma regra de firewall de nível de servidor |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Obtém regras de firewall para um servidor|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modifica uma regra de firewall num servidor|
|[Remover-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Elimina uma regra de firewall de um servidor.|
| New-AzSqlServerVirtualNetworkRule | Cria uma [*regra de rede virtual*](vnet-service-endpoint-rule-overview.md), baseada numa sub-rede que é um ponto final de serviço de Rede Virtual. |

## <a name="the-azure-cli"></a>A CLI do Azure

Para criar e gerir os servidores, bases de dados e firewalls com [o Azure CLI,](/cli/azure)utilize os seguintes comandos [Azure CLI.](/cli/azure/sql/db) Utilize o [Cloud Shell](../../cloud-shell/overview.md) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerir piscinas elásticas, consulte [piscinas elásticas.](elastic-pool-overview.md)

> [!TIP]
> Para um quickstart Azure CLI, consulte [criar uma única Base de Dados Azure SQL utilizando o Azure CLI](az-cli-script-samples-content-guide.md). Para scripts de exemplo Azure CLI, consulte [use CLI para criar uma base de dados na Base de Dados Azure SQL e configurar uma regra de firewall SQL Database](scripts/create-and-configure-database-cli.md) e [use CLI para monitorizar e escalar uma base de dados na Base de Dados Azure SQL](scripts/monitor-and-scale-database-cli.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Cria uma base de dados|
|[lista de az sql db](/cli/azure/sql/db#az_sql_db_list)|Lista todas as bases de dados e armazéns de dados num servidor, ou todas as bases de dados numa piscina elástica|
|[az sql db lista-edições](/cli/azure/sql/db#az_sql_db_list_editions)|Lista objetivos de serviço disponíveis e limites de armazenamento|
|[az sql db lista-usos](/cli/azure/sql/db#az_sql_db_list_usages)|Devoluções de utilizações de bases de dados|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Obtém uma base de dados ou armazém de dados|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Atualiza uma base de dados|
|[az sql db apagar](/cli/azure/sql/db#az_sql_db_delete)|Remove uma base de dados|
|[az group create](/cli/azure/group#az_group_create)|Cria um grupo de recursos|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Cria um servidor|
|[lista de servidores az sql](/cli/azure/sql/server#az_sql_server_list)|Lista servidores|
|[lista de servidores az sql](/cli/azure/sql/server#az_sql_server_list-usages)|Retorna os usos do servidor|
|[show de servidor az sql](/cli/azure/sql/server#az_sql_server_show)|Obtém um servidor|
|[atualização do servidor az sql](/cli/azure/sql/server#az_sql_server_update)|Atualiza um servidor|
|[servidor az sql eliminar](/cli/azure/sql/server#az_sql_server_delete)|Elimina um servidor|
|[az sql servidor firewall-rule criar](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Cria uma regra de firewall do servidor|
|[lista de regras de firewall do servidor az sql](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Lista as regras de firewall num servidor|
|[az sql servidor firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Mostra o detalhe de uma regra de firewall|
|[az sql servidor firewall-rule](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Atualiza uma regra de firewall|
|[az sql servidor firewall-regra eliminar](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Elimina uma regra de firewall|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Para criar e gerir os servidores, bases de dados e firewalls com Transact-SQL, utilize os seguintes comandos T-SQL. Pode emitir estes comandos utilizando o portal Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code,](https://code.visualstudio.com/docs)ou qualquer outro programa que possa ligar-se a um servidor na Base de Dados SQL e passar comandos Transact-SQL. Para gerir piscinas elásticas, consulte [piscinas elásticas.](elastic-pool-overview.md)

> [!TIP]
> Para iniciar um quickstart utilizando o SQL Server Management Studio no Microsoft Windows, consulte [a Base de Dados Azure SQL: Utilize o SQL Server Management Studio para ligar e consultar dados](connect-query-ssms.md). Para iniciar um quickstart utilizando o Código do Estúdio Visual no macOS, Linux ou Windows, consulte [a Base de Dados Azure SQL: Utilize o Código do Estúdio Visual para ligar e consultar dados](connect-query-vscode.md).
> [!IMPORTANT]
> Não é possível criar ou eliminar um servidor utilizando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Cria uma nova base de dados única. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Modifica uma base de dados ou piscina elástica. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Elimina uma base de dados.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devolve a edição (nível de serviço), o objetivo do serviço (nível de preços) e o nome elástico da piscina, caso existam, para Azure SQL Database ou uma piscina de SQL dedicada em Azure Synapse Analytics. Se for iniciado sessão na base de dados principal de um servidor na Base de Dados SQL, retorna a informação em todas as bases de dados. Para a Azure Synapse Analytics, deve estar ligado à base de dados principal.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Devoluções cpu, IO e consumo de memória para uma base de dados na Base de Dados Azure SQL. Existe uma linha por cada 15 segundos, mesmo que não haja atividade na base de dados.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Devolve dados de utilização e armazenamento de CPU para uma base de dados na Base de Dados Azure SQL. Os dados são recolhidos e agregados dentro de intervalos de cinco minutos.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contém estatísticas para eventos de conectividade SQL Database, fornecendo uma visão geral dos sucessos e falhas da ligação da base de dados. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Devolve ligações bem sucedidas da Base de Dados Azure SQL, falhas de ligação e impasses. Pode utilizar estas informações para rastrear ou resolver problemas com a base de dados com a BASE de Dados SQL.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Cria ou atualiza as definições de firewall ao nível do servidor para o seu servidor. Este procedimento armazenado só está disponível na base de dados principal para o início de sessão principal de nível de servidor. Uma regra de firewall ao nível do servidor só pode ser criada usando o Transact-SQL depois de a primeira regra de firewall de nível de servidor ter sido criada por um utilizador com permissões ao nível do Azure|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retorna informações sobre as definições de firewall ao nível do servidor associadas à sua base de dados na Base de Dados Azure SQL.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Remove as definições de firewall ao nível do servidor do servidor. Este procedimento armazenado só está disponível na base de dados principal para o início de sessão principal de nível de servidor.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Cria ou atualiza as regras de firewall ao nível da base de dados para a sua base de dados na Base de Dados Azure SQL. As regras de firewall da base de dados podem ser configuradas para a base de dados principal e para bases de dados de utilizadores na Base de Dados SQL. As regras de firewall da base de dados são úteis quando se utilizam utilizadores de bases de dados contidos. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retorna informações sobre as definições de firewall de nível de base de dados associadas à sua base de dados na Base de Dados Azure SQL. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Remove a definição de firewall de nível de base de dados de uma base de dados. |

## <a name="rest-api"></a>API REST

Para criar e gerir os servidores, bases de dados e firewalls, utilize estes pedidos de API REST.

| Comando | Descrição |
| --- | --- |
|[Servidores - Criar ou atualizar](/rest/api/sql/servers/createorupdate)|Cria ou atualiza um novo servidor.|
|[Servidores - Eliminar](/rest/api/sql/servers/delete)|Elimina um servidor SQL.|
|[Servidores - Obter](/rest/api/sql/servers/get)|Arranja um servidor.|
|[Servidores - Lista](/rest/api/sql/servers/list)|Devolve uma lista de servidores numa subscrição.|
|[Servidores - Lista por grupo de recursos](/rest/api/sql/servers/listbyresourcegroup)|Devolve uma lista de servidores num grupo de recursos.|
|[Servidores - Atualização](/rest/api/sql/servers/update)|Atualiza um servidor existente.|
|[Bases de dados - Criar ou atualizar](/rest/api/sql/databases/createorupdate)|Cria uma nova base de dados ou atualiza uma base de dados existente.|
|[Bases de dados - Eliminar](/rest/api/sql/databases/delete)|Elimina uma base de dados.|
|[Bases de dados - Obter](/rest/api/sql/databases/get)|Tem uma base de dados.|
|[Bases de dados - Lista por piscina elástica](/rest/api/sql/databases/listbyelasticpool)|Devolve uma lista de bases de dados numa piscina elástica.|
|[Bases de dados - Lista por servidor](/rest/api/sql/databases/listbyserver)|Retorna uma lista de bases de dados num servidor.|
|[Bases de dados - Atualização](/rest/api/sql/databases/update)|Atualiza uma base de dados existente.|
|[Regras de firewall - Criar ou atualizar](/rest/api/sql/firewallrules/createorupdate)|Cria ou atualiza uma regra de firewall.|
|[Regras de firewall - Eliminar](/rest/api/sql/firewallrules/delete)|Elimina uma regra de firewall.|
|[Regras de firewall - Obter](/rest/api/sql/firewallrules/get)|Tem uma regra de firewall.|
|[Regras de firewall - Lista por servidor](/rest/api/sql/firewallrules/listbyserver)|Devolve uma lista de regras de firewall.|

## <a name="next-steps"></a>Passos seguintes

- Para saber sobre a migração de uma base de dados do SQL Server para Azure, consulte [a Migração para a Base de Dados SQL Azure](migrate-to-database-from-sql-server.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](features-comparison.md).

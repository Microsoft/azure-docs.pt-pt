---
title: Criar, gerir servidores e bases de dados únicas
description: Saiba sobre a criação e gestão de servidores de base de dados SQL e bases de dados únicas.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 02c4d7ba545282e3654f3889dd8000af33c728c7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359800"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>Crie e gerencie servidores de base de dados SQL e bases de dados únicas na Base de Dados Azure SQL

Pode criar e gerir servidores de base de dados SQL e bases de dados únicas utilizando o portal Azure, PowerShell, Azure CLI, REST API e Transact-SQL.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Portal Azure: Gerir servidores de base de dados SQL e bases de dados únicas

Pode criar o grupo de recursos da base de dados Azure SQL com antecedência ou durante a criação do próprio servidor. Existem múltiplos métodos para chegar a um novo formulário de servidor SQL, quer através da criação de um novo servidor SQL, quer como parte da criação de uma nova base de dados.

### <a name="create-a-blank-sql-database-server"></a>Criar um servidor de base de dados SQL em branco

Para criar um servidor de base de dados SQL utilizando o [portal Azure,](https://portal.azure.com)navegue para um servidor SQL em branco (servidor lógico).  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Criar uma base de dados única SQL em branco ou amostra

Para criar uma base de dados única Azure SQL utilizando o [portal Azure,](https://portal.azure.com)navegue para um formulário de Base de Dados SQL em branco e forneça as informações solicitadas. Pode criar o grupo de recursos da base de dados Azure SQL e o servidor de base de dados SQL com antecedência ou durante a criação da própria base de dados. Pode criar uma base de dados em branco ou criar uma base de dados de amostras baseada na Adventure Works LT.

  ![criar base de dados-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Para obter informações sobre a seleção do nível de preços para a sua base de dados, consulte o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o modelo de compra baseado em [vCore](sql-database-service-tiers-vcore.md).

Para criar uma Instância Gerida, consulte [Criar uma Instância Gerida](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>Gerir um servidor de base de dados SQL existente

Para gerir um servidor de base de dados SQL existente, navegue para o servidor utilizando uma série de métodos - como a partir de uma página específica da base de dados SQL, da página de **servidores SQL** ou da página **De todos os recursos.**

Para gerir uma base de dados existente, navegue para a página de bases de **dados SQL** e clique na base de dados que pretende gerir. A imagem que se segue mostra como começar a definir uma firewall ao nível do servidor para uma base de dados a partir da página **'Overview'** para uma base de dados.

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Para configurar propriedades de desempenho para uma base de dados, consulte [o modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o modelo de compra baseado em [vCore](sql-database-service-tiers-vcore.md).
> [!TIP]
> Para um portal Azure quickstart, consulte [Criar uma base de dados Azure SQL no portal Azure](sql-database-single-database-get-started.md).

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell: Gerir servidores de base de dados SQL e bases de dados únicas

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerir servidores de base de dados Azure SQL, bases de dados individuais e agrofadas e firewalls de servidor estoque SQL com o Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se precisar de instalar ou atualizar o PowerShell, consulte instalar o [módulo PowerShell Azure](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo powerShell, consulte use PowerShell para criar uma base de [dados única Azure SQL e configurar uma regra](scripts/sql-database-create-and-configure-database-powershell.md) de firewall do servidor de base de dados SQL e monitorizar e escalar uma base de dados única [SQL usando powerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

| Cmdlet | Descrição |
| --- | --- |
|[Nova AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Cria uma base de dados |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Obtém uma ou mais bases de dados|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Define propriedades para uma base de dados, ou move uma base de dados existente para um pool elástico|
|[Remover-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Remove uma base de dados|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Cria um grupo de recursos|
|[Novo AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Cria um servidor|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Devolve informações sobre servidores|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Modifica propriedades de um servidor|
|[Remover-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Remove um servidor|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Cria uma regra de firewall ao nível do servidor |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Obtém regras de firewall para um servidor|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modifica uma regra de firewall num servidor|
|[Remover-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Elimina uma regra de firewall de um servidor.|
| New-AzSqlServerVirtualNetworkRule | Cria uma regra de [*rede virtual,* ](sql-database-vnet-service-endpoint-rule-overview.md)baseada numa subnet que é um ponto final de serviço de Rede Virtual. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Azure CLI: Gerir servidores de base de dados SQL e bases de dados únicas

Para criar e gerir o servidor, bases de dados e firewalls Azure SQL com [o Azure CLI,](/cli/azure)utilize os seguintes comandos de base de [dados Azure CLI SQL.](/cli/azure/sql/db) Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerir piscinas elásticas, consulte [piscinas elásticas.](sql-database-elastic-pool.md)

> [!TIP]
> Para um quickstart Azure CLI, consulte Criar uma base de [dados única Azure SQL utilizando o Azure CLI](sql-database-cli-samples.md). Para scripts de exemplo Azure CLI, consulte o Use CLI para criar uma base de [dados única Azure SQL e configurar uma regra](scripts/sql-database-create-and-configure-database-cli.md) de firewall de base de dados SQL e utilizar o CLI para monitorizar e escalar uma base de dados única [Azure SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Cria uma base de dados|
|[az sql db lista](/cli/azure/sql/db#az-sql-db-list)|Lista todas as bases de dados e armazéns de dados num servidor, ou todas as bases de dados num pool elástico|
|[az sql db lista-edições](/cli/azure/sql/db#az-sql-db-list-editions)|Listas de objetivos de serviço disponíveis e limites de armazenamento|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Devoluções de utilizações da base de dados|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Obtém uma base de dados ou armazém de dados|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Atualiza uma base de dados|
|[az sql db excluir](/cli/azure/sql/db#az-sql-db-delete)|Remove uma base de dados|
|[az group create](/cli/azure/group#az-group-create)|Cria um grupo de recursos|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Cria um servidor|
|[lista de servidor esql az](/cli/azure/sql/server#az-sql-server-list)|Lista seleções de servidores|
|[az sql servidor-utilizações](/cli/azure/sql/server#az-sql-server-list-usages)|Devoluções de utilização do servidor|
|[az sql servidor mostrar](/cli/azure/sql/server#az-sql-server-show)|Obtém um servidor|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|Atualiza um servidor|
|[az sql servidor excluir](/cli/azure/sql/server#az-sql-server-delete)|Elimina um servidor|
|[az sql servidor firewall-rule criar](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Cria uma regra de firewall do servidor|
|[az sql lista de firewall-rule do servidor az sql](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Lista as regras de firewall num servidor|
|[az sql servidor firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Mostra o detalhe de uma regra de firewall|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Atualiza uma regra de firewall|
|[az sql servidor firewall-rule excluir](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Elimina uma regra de firewall|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: Gerir servidores de base de dados SQL e bases de dados únicas

Para criar e gerir o servidor, bases de dados e firewalls Azure SQL com Transact-SQL, utilize os seguintes comandos T-SQL. Pode emitir estes comandos utilizando o portal Azure, [O Estúdio de Gestão de Servidores SQL,](/sql/ssms/use-sql-server-management-studio) [Código de Estúdio Visual,](https://code.visualstudio.com/docs)ou qualquer outro programa que possa ligar-se a um servidor de base de dados Azure SQL e passar comandos Transact-SQL. Para gerir piscinas elásticas, consulte [piscinas elásticas.](sql-database-elastic-pool.md)

> [!TIP]
> Para um arranque rápido utilizando o Estúdio de Gestão de Servidores SQL no Microsoft Windows, consulte a Base de [Dados Azure SQL: Utilize o Estúdio de Gestão de Servidores SQL para conectar e consultar dados](sql-database-connect-query-ssms.md). Para um início rápido utilizando o Código do Estúdio Visual no macOS, Linux ou Windows, consulte a Base de [Dados Azure SQL: Use](sql-database-connect-query-vscode.md)o Código do Estúdio Visual para ligar e consultar dados .
> [!IMPORTANT]
> Não é possível criar ou eliminar um servidor utilizando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Cria uma nova base de dados única. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [ALTER DATABASE (Base de Dados Azure SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Modifica uma base de dados Azure SQL. |
|[BASE DE DADOS DROP (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Elimina uma base de dados.|
|[sys.database_service_objetives (Base de Dados Azure SQL)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devolve a edição (nível de serviço), o objetivo de serviço (nível de preços) e o nome da piscina elástica, se houver, para uma base de dados Azure SQL ou um Armazém de Dados Azure SQL. Se iniciar sessão na base de dados principal de um servidor de base de dados Azure SQL, devolve informações em todas as bases de dados. Para o Azure SQL Data Warehouse, deve estar ligado à base de dados principal.|
|[sys.dm_db_resource_stats (Base de Dados Azure SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Devolve CPU, IO e consumo de memória para uma base de dados Azure SQL. Existe uma linha por cada 15 segundos, mesmo que não haja atividade na base de dados.|
|[sys.resource_stats (Base de Dados Azure SQL)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Devolve dados de utilização e armazenamento de CPU para uma Base de Dados Azure SQL. Os dados são recolhidos e agregados dentro de intervalos de cinco minutos.|
|[sys.database_connection_stats (Base de Dados Azure SQL)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contém estatísticas para eventos de conectividade de base de dados SQL Database, fornecendo uma visão geral dos sucessos e falhas de ligação da base de dados. |
|[sys.event_log (Base de Dados Azure SQL)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Devolve ligações de base de dados Azure SQL bem sucedidas, falhas de ligação e impasses. Pode utilizar estas informações para rastrear ou resolver problemas a sua atividade de base de dados com a Base de Dados SQL.|
|[sp_set_firewall_rule (Base de Dados Azure SQL)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Cria ou atualiza as definições de firewall do nível do servidor para o seu servidor de base de dados SQL. Este procedimento armazenado só está disponível na base de dados principal para o login principal ao nível do servidor. Uma regra de firewall ao nível do servidor só pode ser criada usando transact-SQL depois de a primeira regra de firewall de nível de servidor ter sido criada por um utilizador com permissões de nível Azure|
|[sys.firewall_rules (Base de Dados Azure SQL)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Devolve informações sobre as definições de firewall ao nível do servidor associadas à sua Base de Dados SQL Microsoft Azure.|
|[sp_delete_firewall_rule (Base de Dados Azure SQL)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Remove as definições de firewall do nível do servidor do seu servidor de base de dados SQL. Este procedimento armazenado só está disponível na base de dados principal para o login principal ao nível do servidor.|
|[sp_set_database_firewall_rule (Base de Dados Azure SQL)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Cria ou atualiza as regras de firewall de nível de base de dados para a sua Base de Dados Azure SQL ou Armazém de Dados SQL. As regras de firewall da base de dados podem ser configuradas para a base de dados principal e para bases de dados de utilizadores na Base de Dados SQL. As regras de firewall da base de dados são úteis quando se utilizam utilizadores de bases de dados contidos. |
|[sys.database_firewall_rules (Base de Dados Azure SQL)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Devolve informações sobre as definições de firewall de nível de base de dados associadas à sua Base de Dados SQL Microsoft Azure. |
|[sp_delete_database_firewall_rule (Base de Dados Azure SQL)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Remove a definição de firewall ao nível da base de dados da sua Base de Dados Azure SQL ou do Armazém de Dados SQL. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>REST API: Gerir servidores de base de dados SQL e bases de dados únicas

Para criar e gerir o servidor, bases de dados e firewalls do Azure SQL, utilize estes pedidos REST API.

| Comando | Descrição |
| --- | --- |
|[Servidores - Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Cria ou atualiza um novo servidor.|
|[Servidores - Eliminar](https://docs.microsoft.com/rest/api/sql/servers/delete)|Elimina um servidor SQL.|
|[Servidores - Obter](https://docs.microsoft.com/rest/api/sql/servers/get)|Arranja um servidor.|
|[Servidores - Lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Devolve uma lista de servidores numa subscrição.|
|[Servidores - Lista por grupo de recursos](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Devolve uma lista de servidores num grupo de recursos.|
|[Servidores - Atualização](https://docs.microsoft.com/rest/api/sql/servers/update)|Atualiza um servidor existente.|
|[Bases de dados - Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Cria uma nova base de dados ou atualiza uma base de dados existente.|
|[Bases de dados - Eliminar](https://docs.microsoft.com/rest/api/sql/databases/delete)|Elimina uma base de dados.|
|[Bases de dados - Obter](https://docs.microsoft.com/rest/api/sql/databases/get)|Tem uma base de dados.|
|[Bases de dados - Lista por piscina elástica](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Devolve uma lista de bases de dados numa piscina elástica.|
|[Bases de dados - Lista por servidor](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Devolve uma lista de bases de dados num servidor.|
|[Bases de dados - Atualização](https://docs.microsoft.com/rest/api/sql/databases/update)|Atualiza uma base de dados existente.|
|[Regras de firewall - Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Cria ou atualiza uma regra de firewall.|
|[Regras de firewall - Eliminar](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Elimina uma regra de firewall.|
|[Regras de firewall - Obter](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Tem uma regra de firewall.|
|[Regras de firewall - Lista por servidor](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Devolve uma lista de regras de firewall.|

## <a name="next-steps"></a>Passos seguintes

- Para saber sobre a migração de uma base de dados do SQL Server para o Azure, consulte [migrate para azure SQL Database](sql-database-single-database-migrate.md).
- Para obter informações sobre as funcionalidades suportadas, veja [Funcionalidades](sql-database-features.md).

---
title: Servidores
description: Conheça os servidores de Base de Dados Azure SQL e a sua gestão.
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
ms.openlocfilehash: 7557ed43d9ecb8fc7a584e7e8239bc7ccb972e99
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75647174"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Servidores de base de dados Azure SQL e sua gestão

## <a name="what-is-an-azure-sql-database-server"></a>O que é um servidor de base de dados Azure SQL

Um servidor de base de dados SQL é uma construção lógica que funciona como um ponto administrativo central para múltiplas bases de dados individuais ou [agruparadas,](sql-database-elastic-pool.md) [logins,](sql-database-manage-logins.md)regras de [firewall, regras](sql-database-firewall-configure.md) [de auditoria,](sql-database-auditing.md)políticas de deteção de [ameaças,](sql-database-threat-detection.md)e os [grupos](sql-database-auto-failover-group.md) de dados Failover A SQL Database server pode estar numa região diferente do seu grupo de recursos. O servidor de base de dados SQL deve existir antes de poder criar a base de dados Azure SQL. Todas as bases de dados geridas por um servidor de base de dados SQL são criadas na mesma região que o servidor de base de dados SQL.

Um servidor de base de dados SQL é distinto de uma instância do SQL Server que você pode estar familiarizado no mundo no local. Especificamente, o serviço De base de dados SQL não garante a localização das bases de dados em relação ao servidor de base de dados SQL que as gere, e não expõe nenhum acesso ou funcionalidade sem acesso ou funcionalidades ao nível de instância. Em contraste, as bases de dados de instâncias em caso de gestão são todas co-localizadas - da mesma forma que você está familiarizado com o SQL Server no mundo no local.

Quando cria um servidor de base de dados SQL, fornece uma conta de login do servidor e uma palavra-passe que tem direitos administrativos para a base de dados principal desse servidor e todas as bases de dados criadas nesse servidor. Esta conta inicial é uma conta de login SQL. A Base de Dados Azure SQL suporta a autenticação SQL e a autenticação do diretório Ativo Azure para autenticação. Para obter informações sobre logins e autenticação, consulte [Managing Databases e Logins na Base de Dados Azure SQL](sql-database-manage-logins.md). A autenticação do Windows não é suportada.

Um servidor de base de dados SQL:

- É criado numa subscrição do Azure, mas pode ser movido com os recursos nele contidos para outra subscrição
- É o recurso principal para bases de dados, conjuntos elásticos e armazéns de dados
- Fornece um espaço de nome para bases de dados, piscinas elásticas e armazéns de dados
- É um recipiente lógico com semântica vitalício forte - elimine um servidor e apaga as bases de dados contidas, piscinas elásticas e armazéns de dados
- Participa no [controlo de acesso baseado em funções do Azure (RBAC)](/azure/role-based-access-control/overview) - bases de dados, piscinas elásticas e armazéns de dados dentro de um servidor herdam direitos de acesso do servidor
- É um elemento de alta ordem da identidade de bases de dados, piscinas elásticas e armazéns de dados para fins de gestão de recursos Azure (ver o sistema de URL para bases de dados e piscinas)
- Coloca recursos numa região
- Fornece um ponto final de ligação para o acesso à base de dados (`<serverName>`.database.windows.net)
- Fornece acesso a metadados relativamente aos recursos contidos através de DMVs, estabelecendo ligação a uma base de dados mestra
- Fornece o âmbito para políticas de gestão aplicáveis às suas bases de dados - logins, firewall, auditoria, deteção de ameaças, e tais
- É restringido por uma quota dentro da subscrição-mãe (seis servidores por subscrição por padrão - [consulte os limites](../azure-resource-manager/management/azure-subscription-service-limits.md)de subscrição aqui )
- Fornece o âmbito de aplicação para quotas de base de dados e quota DTU ou vCore para os recursos que contém (tais como 45.000 DTU)
- É o âmbito de versão para capacidades ativadas em recursos contidos
- Os inícios de sessão principais ao nível do servidor podem gerir todas as bases de dados num servidor
- Pode conter inícios de sessão semelhantes àqueles nas instâncias do SQL Server no local, aos quais é concedido acesso a uma ou mais bases de dados no servidor e aos quais podem ser concedidos direitos administrativos limitados. Para obter mais informações, veja [Inícios de sessão](sql-database-manage-logins.md).
- A colagem padrão para todas as bases de `SQL_LATIN1_GENERAL_CP1_CI_AS`dados `LATIN1_GENERAL` criadas num servidor `CP1` de base de dados `CI` SQL é, `AS` onde está inglês (Estados Unidos), é a página de código 1252, é insensível a casos e é sensível ao sotaque.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Gerir servidores, bases de dados e firewalls Azure SQL utilizando o portal Azure

Pode criar o grupo de recursos da base de dados Azure SQL com antecedência ou durante a criação do próprio servidor. Existem múltiplos métodos para chegar a um novo formulário de servidor SQL, quer através da criação de um novo servidor SQL, quer como parte da criação de uma nova base de dados.

### <a name="create-a-blank-sql-database-server"></a>Criar um servidor de base de dados SQL em branco

Para criar um servidor de base de dados Azure SQL (sem base de dados) utilizando o [portal Azure,](https://portal.azure.com)navegue para um servidor SQL em branco (servidor lógico).  

### <a name="create-a-blank-or-sample-sql-database"></a>Criar uma base de dados SQL em branco ou amostra

Para criar uma base de dados Azure SQL utilizando o [portal Azure,](https://portal.azure.com)navegue para um formulário de Base de Dados SQL em branco e forneça as informações solicitadas. Pode criar o grupo de recursos da base de dados Azure SQL e o servidor de base de dados SQL com antecedência ou durante a criação da própria base de dados. Pode criar uma base de dados em branco ou criar uma base de dados de amostras baseada na Adventure Works LT.

  ![criar base de dados-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Para obter informações sobre a seleção do nível de preços para a sua base de dados, consulte o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o modelo de compra baseado em [vCore](sql-database-service-tiers-vcore.md).

Para criar um caso gerido, consulte [Criar um caso gerido](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Gerir um servidor SQL existente

Para gerir um servidor existente, navegue para o servidor utilizando uma série de métodos - como a partir de uma página específica da base de dados SQL, da página de **servidores SQL** ou da página **All resources.**

Para gerir uma base de dados existente, navegue para a página de bases de **dados SQL** e clique na base de dados que pretende gerir. A imagem que se segue mostra como começar a definir uma firewall ao nível do servidor para uma base de dados a partir da página **'Overview'** para uma base de dados.

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Para configurar propriedades de desempenho para uma base de dados, consulte [o modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o modelo de compra baseado em [vCore](sql-database-service-tiers-vcore.md).
> [!TIP]
> Para um portal Azure quickstart, consulte [Criar uma base de dados Azure SQL no portal Azure](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Gerir servidores, bases de dados e firewalls Azure SQL usando powerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerir o servidor, bases de dados e firewalls Azure SQL com o Azure PowerShell, utilize os seguintes cmdlets PowerShell. Se precisar de instalar ou atualizar o PowerShell, consulte instalar o [módulo PowerShell Azure](/powershell/azure/install-az-ps). Para criar e gerir piscinas elásticas, consulte [piscinas elásticas.](sql-database-elastic-pool.md)

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
| New-AzSqlServerVirtualNetworkRule | Cria uma regra de [*rede virtual,*](sql-database-vnet-service-endpoint-rule-overview.md)baseada numa subnet que é um ponto final de serviço de Rede Virtual. |

> [!TIP]
> Para um arranque rápido powerShell, consulte Criar uma base de [dados única Azure SQL utilizando powerShell](sql-database-single-database-get-started.md). Para scripts de exemplo PowerShell, consulte use powerShell para criar uma base de [dados única Azure SQL e configurar uma regra](scripts/sql-database-create-and-configure-database-powershell.md) de firewall e [monitore e dimensione uma base de dados única Azure SQL usando powerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Gerir servidores, bases de dados e firewalls Azure SQL utilizando o Azure CLI

Para criar e gerir o servidor, bases de dados e firewalls Azure SQL com o [Azure CLI,](/cli/azure)utilize os seguintes comandos de base de [dados Azure CLI SQL.](/cli/azure/sql/db) Utilize o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu browser ou [instale-a](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerir piscinas elásticas, consulte [piscinas elásticas.](sql-database-elastic-pool.md)

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

> [!TIP]
> Para um quickstart Azure CLI, consulte Criar uma base de [dados única Azure SQL utilizando o Azure CLI](sql-database-cli-samples.md). Para scripts de exemplo Azure CLI, consulte o Use CLI para criar uma base de [dados única Azure SQL e configurar uma regra](scripts/sql-database-create-and-configure-database-cli.md) de firewall e utilizar o CLI para monitorizar e escalar uma base de dados única [Azure SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Gerir servidores, bases de dados e firewalls Azure SQL usando transact-SQL

Para criar e gerir o servidor, bases de dados e firewalls Azure SQL com Transact-SQL, utilize os seguintes comandos T-SQL. Pode emitir estes comandos utilizando o portal Azure, [O Estúdio de Gestão de Servidores SQL,](/sql/ssms/use-sql-server-management-studio) [Código de Estúdio Visual,](https://code.visualstudio.com/docs)ou qualquer outro programa que possa ligar-se a um servidor de base de dados Azure SQL e passar comandos Transact-SQL. Para gerir piscinas elásticas, consulte [piscinas elásticas.](sql-database-elastic-pool.md)

> [!IMPORTANT]
> Não é possível criar ou eliminar um servidor utilizando o Transact-SQL.
>

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE (Base de Dados Azure SQL)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria uma nova base de dados. Tem de estar ligado à base de dados principal para criar uma nova base de dados.|
| [ALTER DATABASE (Base de Dados Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica uma base de dados Azure SQL. |
|[ALTER DATABASE (Armazém de Dados Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modifica um Armazém de Dados Azure SQL.|
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

> [!TIP]
> Para um arranque rápido utilizando o Estúdio de Gestão de Servidores SQL no Microsoft Windows, consulte a Base de [Dados Azure SQL: Utilize o Estúdio de Gestão de Servidores SQL para conectar e consultar dados](sql-database-connect-query-ssms.md). Para um início rápido utilizando o Código do Estúdio Visual no macOS, Linux ou Windows, consulte a Base de [Dados Azure SQL: Use](sql-database-connect-query-vscode.md)o Código do Estúdio Visual para ligar e consultar dados .

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Gerir servidores, bases de dados e firewalls Azure SQL utilizando a API REST

Para criar e gerir o servidor, bases de dados e firewalls do Azure SQL, utilize estes pedidos REST API.

| Comando | Descrição |
| --- | --- |
|[Servidores - Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Cria ou atualiza um novo servidor.|
|[Servidores - Eliminar](https://docs.microsoft.com/rest/api/sql/servers/delete)|Elimina um servidor SQL.|
|[Servidores - Obter](https://docs.microsoft.com/rest/api/sql/servers/get)|Arranja um servidor.|
|[Servidores - Lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Devolve uma lista de servidores.|
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

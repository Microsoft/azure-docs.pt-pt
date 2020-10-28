---
title: Regras de firewall IP
description: Configure as regras de firewall IP ao nível do servidor para uma base de dados na Base de Dados Azure SQL ou na firewall Azure Synapse Analytics. Gerir o acesso e configurar as regras de firewall IP de nível de base de dados para a Base de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: 802c126548a6fa7062a262e2f939c9a214480794
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789646"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database e regras de firewall IP Azure Synapse
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Quando cria um novo servidor na Base de Dados Azure SQL ou no Azure Synapse Analytics nomeado *mysqlserver* , por exemplo, uma firewall de nível de servidor bloqueia todo o acesso ao ponto final público do servidor (que é acessível *a mysqlserver.database.windows.net).* Para simplificar, a *BASE de Dados SQL* é utilizada para se referir tanto à Base de Dados SQL como à Azure Synapse Analytics (antiga SQL Data Warehouse).

> [!IMPORTANT]
> Este artigo *não* se aplica a *Azure SQL Managed Instance* . Para obter informações sobre a configuração da rede, consulte [Ligar a sua aplicação a Azure SQL Managed Instance](../managed-instance/connect-application-instance.md).
>
> O Azure Synapse suporta apenas as regras de firewall IP ao nível do servidor. Não suporta regras de firewall IP ao nível da base de dados.

## <a name="how-the-firewall-works"></a>Como funciona a firewall

As tentativas de ligação da internet e do Azure devem passar pela firewall antes de chegarem ao servidor ou à base de dados, como mostra o diagrama seguinte.

   ![Diagrama de configuração de firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Regras de firewall de IP ao nível do servidor

Estas regras permitem que os clientes acedam a todo o servidor, ou seja, a todas as bases de dados geridas pelo servidor. As regras estão guardadas na base *de dados principal.* Pode ter um máximo de 128 regras de firewall de IP ao nível do servidor para um servidor. Se tiver os **Serviços e recursos de Allow Azure para aceder a esta** definição de servidor ativada, esta conta como uma única regra de firewall para o servidor.
  
Pode configurar as regras de firewall IP ao nível do servidor utilizando as declarações do portal Azure, PowerShell ou Transact-SQL.

- Para utilizar o portal ou PowerShell, tem de ser o proprietário da subscrição ou um contribuinte de subscrição.
- Para utilizar o Transact-SQL, tem de ligar-se à base de dados *principal* como o login principal de nível do servidor ou como administrador do Azure Ative Directory. (Uma regra de firewall IP ao nível do servidor deve ser criada primeiro por um utilizador que tenha permissões ao nível do Azure.)

### <a name="database-level-ip-firewall-rules"></a>Regras de firewall IP ao nível da base de dados

As regras de firewall IP ao nível da base de dados permitem aos clientes aceder a determinadas bases de dados (seguras). Cria-se as regras para cada base de dados (incluindo a base de dados *principal)* e estão guardadas na base de dados individual.
  
- Só é possível criar e gerir regras de firewall IP ao nível da base de dados para bases de dados de mestres e utilizadores utilizando declarações Transact-SQL e apenas depois de configurar a primeira firewall ao nível do servidor.
- Se especificar um intervalo de endereços IP na regra de firewall IP de nível de base de dados que está fora do alcance na regra de firewall IP de nível de servidor, apenas os clientes que tenham endereços IP na gama de nível de base de dados podem aceder à base de dados.
- Pode ter um máximo de 128 regras de firewall IP de nível de base de dados para uma base de dados. Para obter mais informações sobre a configuração das regras de firewall IP ao nível da base de dados, consulte o exemplo mais tarde neste artigo e consulte [sp_set_database_firewall_rule (Base de Dados Azure SQL)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Recomendações para definir regras de firewall

Recomendamos que utilize regras de firewall IP ao nível da base de dados sempre que possível. Esta prática aumenta a segurança e torna a base de dados mais portátil. Utilize regras de firewall IP ao nível do servidor para administradores. Também as utilize quando tiver muitas bases de dados que tenham os mesmos requisitos de acesso, e não queira configurar cada base de dados individualmente.

> [!NOTE]
> Para obter informações sobre bases de dados portáteis no contexto de continuidade do negócio, veja [Authentication requirements for disa ster recovery (Requisitos de autenticação para a recuperação após desastre)](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regras de firewall de IP ao nível de servidor vs. ao nível da base de dados

*Os utilizadores de uma base de dados devem ser totalmente isolados de outra base de dados?*

Se *sim,* utilize regras de firewall IP de nível de base de dados para conceder acesso. Este método evita a utilização de regras de firewall IP de nível de servidor, que permitem o acesso através da firewall a todas as bases de dados. Isso reduziria a profundidade das suas defesas.

*Os utilizadores nos endereços IP precisam de acesso a todas as bases de dados?*

Se *sim,* utilize as regras de firewall IP ao nível do servidor para reduzir o número de vezes que tem de configurar as regras de firewall IP.

*A pessoa ou equipa que configura as regras de firewall IP só tem acesso através do portal Azure, PowerShell ou da API REST?*

Em caso afirmativo, deve utilizar as regras de firewall IP ao nível do servidor. As regras de firewall IP ao nível da base de dados só podem ser configuradas através do Transact-SQL.  

*A pessoa ou equipa que configura as regras de firewall IP está proibida de ter permissão de alto nível ao nível da base de dados?*

Em caso afirmativo, utilize as regras de firewall IP ao nível do servidor. Precisa, pelo menos, de obter permissão *de BASE DE DADOS* no nível da base de dados para configurar as regras de firewall IP de nível de base de dados através da Transact-SQL.  

*A pessoa ou equipa que configura ou audita as regras de firewall IP gere centralmente as regras de firewall IP para muitas (talvez centenas) de bases de dados?*

Neste cenário, as melhores práticas são determinadas pelas suas necessidades e ambiente. As regras de firewall IP ao nível do servidor podem ser mais fáceis de configurar, mas a script pode configurar regras ao nível da base de dados. E mesmo que utilize regras de firewall IP ao nível do servidor, poderá precisar de auditar as regras de firewall IP ao nível da base de dados para ver se os utilizadores com permissão *CONTROL* na base de dados criam regras de firewall IP de nível de base de dados.

*Posso usar uma mistura de regras de firewall IP ao nível do servidor e de base de dados?*

Sim. Alguns utilizadores, como administradores, podem precisar de regras de firewall IP ao nível do servidor. Outros utilizadores, como os utilizadores de uma aplicação de base de dados, podem necessitar de regras de firewall IP ao nível da base de dados.

### <a name="connections-from-the-internet"></a>Ligações a partir da internet

Quando um computador tenta ligar-se ao seu servidor a partir da internet, a firewall verifica primeiro o endereço IP originário do pedido contra as regras de firewall IP de nível de base de dados para a base de dados que a ligação solicita.

- Se o endereço estiver dentro de um intervalo especificado nas regras de firewall IP de nível de base de dados, a ligação é concedida à base de dados que contém a regra.
- Se o endereço não estiver dentro de um intervalo nas regras de firewall IP de nível de base de dados, a firewall verifica as regras de firewall IP de nível de servidor. Se o endereço estiver dentro de um intervalo que está nas regras de firewall IP de nível de servidor, a ligação é concedida. As regras de firewall IP de nível de servidor aplicam-se a todas as bases de dados geridas pelo servidor.  
- Se o endereço não estiver dentro de um intervalo que esteja em qualquer uma das regras de firewall IP de nível de base ou de servidor, o pedido de ligação falha.

> [!NOTE]
> Para aceder à Base de Dados Azure SQL a partir do seu computador local, certifique-se de que a firewall da sua rede e computador local permitem a comunicação de saída na porta TCP 1433.

### <a name="connections-from-inside-azure"></a>Ligações de dentro do Azure

Para permitir que as aplicações hospedadas dentro do Azure se conectem ao seu servidor SQL, as ligações Azure devem ser ativadas. Quando uma aplicação do Azure tenta ligar-se ao seu servidor, a firewall verifica se as ligações Azure são permitidas. Isto pode ser ligado diretamente a partir da lâmina do portal Azure, definindo as regras de Firewall, bem como alternando os **Serviços e recursos de Allow Azure para aceder a este servidor** a **ON** nas definições de **Firewalls e redes virtuais.** Se a ligação não for permitida, o pedido não chega ao servidor.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações do Azure, incluindo ligações a partir das subscrições de outros clientes. Se selecionar esta opção, certifique-se de que as permissões de login e utilizador limitam o acesso apenas aos utilizadores autorizados.

## <a name="permissions"></a>Permissões

Para poder criar e gerir regras de firewall de IP para o Azure SQL Server, terá de:

- na [função de Contribuinte do Servidor SQL](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- no papel [de Gestor de Segurança SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- o proprietário do recurso que contém o Servidor Azure SQL

## <a name="create-and-manage-ip-firewall-rules"></a>Criar e gerir regras de firewall IP

Cria a primeira definição de firewall ao nível do servidor utilizando o [portal Azure](https://portal.azure.com/) ou programáticamente utilizando [a Azure PowerShell](/powershell/module/az.sql), [Azure CLI](/cli/azure/sql/server/firewall-rule)ou uma [API AZure REST](/rest/api/sql/firewallrules/createorupdate). Cria e gere regras adicionais de firewall IP ao nível do servidor utilizando estes métodos ou Transact-SQL.

> [!IMPORTANT]
> As regras de firewall IP ao nível da base de dados só podem ser criadas e geridas utilizando o Transact-SQL.

Para melhorar o desempenho, as regras de firewall de IP ao nível do servidor são colocadas em cache temporariamente ao nível da base de dados. Para atualizar a cache, veja [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> Pode utilizar [a Auditoria de Base de Dados](../../azure-sql/database/auditing-overview.md) para auditar alterações de firewall ao nível do servidor e ao nível da base de dados.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Utilize o portal Azure para gerir as regras de firewall IP de nível de servidor

Para definir uma regra de firewall IP de nível de servidor no portal Azure, aceda à página geral da sua base de dados ou do seu servidor.

> [!TIP]
> Para obter um tutorial, consulte [Criar uma base de dados utilizando o portal Azure](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>A partir da página geral da base de dados

1. Para definir uma regra de firewall IP de nível de servidor a partir da página de visão geral da base de dados, selecione **Definir firewall** do servidor na barra de ferramentas, como mostra a seguinte imagem.

    ![Regra de firewall IP do servidor](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    A página **de definições de Firewall** para o servidor abre.

2. **Selecione Adicionar** o IP do cliente na barra de ferramentas para adicionar o endereço IP do computador que está a utilizar e, em seguida, selecione **Guardar** . Uma regra de firewall IP de nível de servidor é criada para o seu endereço IP atual.

    ![Definir regra de firewall IP nível de servidor](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>A partir da página geral do servidor

A página geral do seu servidor abre. Mostra o nome do servidor totalmente qualificado (como *mynewserver20170403.database.windows.net)* e fornece opções para uma configuração posterior.

1. Para definir uma regra de nível de servidor a partir desta página, selecione **Firewall** a partir do menu **Definições** no lado esquerdo.

2. **Selecione Adicionar** o IP do cliente na barra de ferramentas para adicionar o endereço IP do computador que está a utilizar e, em seguida, selecione **Guardar** . Uma regra de firewall IP de nível de servidor é criada para o seu endereço IP atual.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Utilize o Transact-SQL para gerir as regras de firewall IP

| Vista de catálogo ou procedimento armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Servidor |Exibe as atuais regras de firewall IP de nível de servidor |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Servidor |Cria ou atualiza as regras de firewall IP de nível de servidor |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Servidor |Remove as regras de firewall IP de nível de servidor |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Base de Dados |Exibe as atuais regras de firewall IP de nível de base de dados |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Base de Dados |Cria ou atualiza as regras de firewall IP de nível de base de dados |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Bases de Dados |Remove regras de firewall IP de nível de base de dados |

O exemplo a seguir revê as regras existentes, permite uma gama de endereços IP no servidor *Contoso,* e elimina uma regra de firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Em seguida, adicione uma regra de firewall IP de nível de servidor.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para eliminar uma regra de firewall IP de nível de servidor, execute o *procedimento sp_delete_firewall_rule* armazenado. O exemplo a seguir elimina a regra *ContosoFirewallRule* :

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Use o PowerShell para gerir as regras de firewall IP de nível de servidor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento é agora para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos nos módulos Az e AzureRm são substancialmente idênticos.

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Servidor |Devolve as regras de firewall ao nível do servidor atuais |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Servidor |Cria uma regra de firewall ao nível do servidor nova |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Servidor |Atualiza as propriedades de uma regra de firewall ao nível do servidor existente |
| [Remover-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Servidor |Remove as regras de firewall ao nível do servidor |

O exemplo a seguir utiliza o PowerShell para definir uma regra de firewall IP de nível de servidor:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Para $servername especificar o nome do servidor e não o nome DNS totalmente qualificado, por exemplo, **especifique o mysqldbserver** em vez de **mysqldbserver.database.windows.net**
>
> Para exemplos powerShell no contexto de um arranque rápido, consulte [Create DB - PowerShell](powershell-script-content-guide.md) e [Criar uma única base de dados e configurar uma regra de firewall IP de nível de servidor utilizando PowerShell](scripts/create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Utilize o CLI para gerir as regras de firewall IP de nível de servidor

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
|[az sql servidor firewall-rule criar](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Servidor|Cria uma regra de firewall IP do servidor|
|[lista de regras de firewall do servidor az sql](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Servidor|Lista as regras de firewall IP num servidor|
|[az sql servidor firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Servidor|Mostra o detalhe de uma regra de firewall IP|
|[az sql servidor firewall-rule](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Servidor|Atualiza uma regra de firewall IP|
|[az sql servidor firewall-regra eliminar](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Servidor|Elimina uma regra de firewall IP|

O exemplo a seguir utiliza o CLI para definir uma regra de firewall IP de nível de servidor:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Para $servername especificar o nome do servidor e não o nome DNS totalmente qualificado, por exemplo, **especifique o mysqldbserver** em vez de **mysqldbserver.database.windows.net**
>
> Para um exemplo de CLI no contexto de um arranque rápido, consulte [Create DB - Azure CLI](az-cli-script-samples-content-guide.md) e [Crie uma única base de dados e configuure uma regra de firewall IP de nível de servidor utilizando o Azure CLI](scripts/create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Utilize uma API REST para gerir as regras de firewall IP de nível de servidor

| API | Nível | Descrição |
| --- | --- | --- |
| [Listar regras de firewall](/rest/api/sql/firewallrules/listbyserver) |Servidor |Exibe as atuais regras de firewall IP de nível de servidor |
| [Criar ou atualizar regras de firewall](/rest/api/sql/firewallrules/createorupdate) |Servidor |Cria ou atualiza as regras de firewall IP de nível de servidor |
| [Eliminar regras de firewall](/rest/api/sql/firewallrules/delete) |Servidor |Remove as regras de firewall IP de nível de servidor |
| [Obtenha regras de firewall](/rest/api/sql/firewallrules/get) | Servidor | Obtém regras de firewall IP de nível de servidor |

## <a name="troubleshoot-the-database-firewall"></a>Resolver problemas da firewall da base de dados

Considere os seguintes pontos quando o acesso à Base de Dados Azure SQL não se comporta como espera.

- **Configuração local de firewall:**

  Antes de o computador poder aceder à Base de Dados Azure SQL, poderá ter de criar uma exceção de firewall no seu computador para a porta TCP 1433. Para fazer ligações dentro do limite da nuvem Azure, poderá ter de abrir portas adicionais. Para mais informações, consulte a secção "SQL Database: Outside vs inside" dos [Portos para além de 1433 para ADO.NET 4.5 e Azure SQL Database](adonet-v12-develop-direct-route-ports.md).

- **Tradução de endereço de rede:**

  Devido à tradução de endereços de rede (NAT), o endereço IP utilizado pelo seu computador para se ligar à Base de Dados Azure SQL pode ser diferente do endereço IP nas definições de configuração IP do seu computador. Para visualizar o endereço IP que o seu computador está a usar para ligar ao Azure:
    1. Inicie sessão no portal.
    1. Aceda ao **separador Configurar** no servidor que acolhe a sua base de dados.
    1. O **Endereço IP do Cliente Atual** é apresentado na secção Endereço IP **Permitido.** Selecione **Adicionar** para **endereços IP permitidos** para permitir que este computador aceda ao servidor.

- **As alterações à lista de autorizações ainda não produziram efeitos:**

  Pode haver um atraso de até cinco minutos para que as alterações à configuração da firewall da Base de Dados Azure SQL entrem em vigor.

- **O login não é autorizado, ou foi utilizada uma senha incorreta:**

  Se um login não tiver permissões no servidor ou a palavra-passe estiver incorreta, a ligação ao servidor é negada. A criação de uma definição de firewall apenas dá aos clientes a *oportunidade* de tentarem ligar-se ao seu servidor. O cliente ainda deve fornecer as credenciais de segurança necessárias. Para obter mais informações sobre a preparação de logins, consulte [Controle e concede acesso à base de dados.](logins-create-manage.md)

- **Endereço IP dinâmico:**

  Se tiver uma ligação à Internet que utilize o endereço IP dinâmico e tiver dificuldade em passar pela firewall, experimente uma das seguintes soluções:
  
  - Peça ao seu fornecedor de serviços de internet a gama de endereços IP que está atribuído aos computadores do seu cliente que acedam ao servidor. Adicione o intervalo de endereço IP como uma regra de firewall IP.
  - Obtenha endereço IP estático em vez dos computadores clientes. Adicione os endereços IP como regras de firewall IP.

## <a name="next-steps"></a>Passos seguintes

- Confirme que o seu ambiente de rede corporativa permite a comunicação de entrada a partir dos intervalos de endereços IP compute (incluindo gamas SQL) que são utilizados pelos datacenters Azure. Poderá ter de adicionar esses endereços IP à lista de autorizações. Consulte [as gamas IP do centro de dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).  
- Para obter um início rápido sobre a criação de uma regra de firewall IP de nível de servidor, consulte [Criar uma única base de dados na Base de Dados Azure SQL](single-database-create-quickstart.md).
- Para obter ajuda para se ligar a uma base de dados na Base de Dados Azure SQL a partir de aplicações de código aberto ou de terceiros, consulte as amostras de [código quickstart do Cliente para a Base de Dados Azure SQL](connect-query-content-reference-guide.md#libraries).
- Para obter informações sobre portas adicionais que possa ter de abrir, consulte a secção "SQL Database: Outside vs inside" dos [Portos para além de 1433 para ADO.NET 4.5 e BASE de Dados SQL](adonet-v12-develop-direct-route-ports.md)
- Para obter uma visão geral da segurança da Base de Dados Azure SQL, consulte [a sua base de dados](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
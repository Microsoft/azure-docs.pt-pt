---
title: Regras de firewall IP
description: Configure as regras de firewall IP de nível de servidor para uma base de dados SQL ou firewall SQL Data Warehouse. Gerencie as regras de firewall IP de nível de base de dados para uma única ou piscina de bases de dados.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9db6b5ff517a1b0d67e59591ee634dfad685527b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269006"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Base de Dados Azure SQL e regras de firewall IP do Armazém de Dados Azure SQL

> [!NOTE]
> Este artigo aplica-se aos servidores Azure SQL e tanto às bases de dados azure SQL como ao Azure SQL Data Warehouse num servidor Azure SQL. Para a simplicidade, a Base de *Dados SQL* é usada para se referir tanto à Base de Dados SQL como ao Armazém de Dados SQL.

> [!IMPORTANT]
> Este artigo *não* se aplica à instância gerida pela Base de *Dados Azure SQL*. Para obter informações sobre a configuração da rede, consulte Ligue a sua aplicação à instância gerida pela Base de [Dados Azure SQL](sql-database-managed-instance-connect-app.md).

Quando cria um novo servidor Azure SQL chamado *mysqlserver*, por exemplo, a firewall SQL Database bloqueia todo o acesso ao ponto final público para o servidor (que é acessível a *mysqlserver.database.windows.net).*

> [!IMPORTANT]
> O SQL Data Warehouse apenas suporta as regras de firewall IP de nível de servidor. Não suporta regras de firewall IP de nível de base de dados.

## <a name="how-the-firewall-works"></a>Como funciona a firewall
As tentativas de ligação a partir da internet e do Azure devem passar pela firewall antes de chegarem ao seu servidor SQL ou à base de dados SQL, como mostra o diagrama seguinte.

   ![Diagrama de configuração de firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Regras de firewall de IP ao nível do servidor

  Estas regras permitem aos clientes aceder a todo o seu servidor Azure SQL, ou seja, todas as bases de dados dentro do mesmo servidor de base de dados SQL. As regras estão guardadas na base de dados *principal.* Pode ter um máximo de 128 regras de firewall IP de nível de servidor para um Servidor Azure SQL.
  
  Pode configurar as regras de firewall IP ao nível do servidor utilizando as declarações do portal Azure, PowerShell ou Transact-SQL.
  - Para utilizar o portal ou powerShell, deve ser o proprietário da subscrição ou um colaborador de subscrição.
  - Para utilizar o Transact-SQL, deve ligar-se à instância de Base de Dados SQL como o login principal de nível de servidor ou como administrador do Diretório Ativo Azure. (Uma regra de firewall IP ao nível do servidor deve ser criada primeiro por um utilizador que tenha permissões de nível Azure.)

### <a name="database-level-ip-firewall-rules"></a>Regras de firewall IP de nível de base de dados

  Estas regras permitem aos clientes aceder a determinadas bases de dados (seguras) dentro do mesmo servidor de base de dados SQL. Cria as regras para cada base de dados (incluindo a base de dados *principal)* e são armazenadas na base de dados individual.
  
  Só é possível criar e gerir regras de firewall IP de nível de base de dados para bases de dados master e user, utilizando declarações Transact-SQL e apenas depois de configurar a primeira firewall de nível de servidor.
  
  Se especificar um intervalo de endereçoIP na regra de firewall IP de nível de base de dados que está fora do alcance da regra de firewall IP de nível de servidor, apenas os clientes que possuem endereços IP na gama de nível de base de dados podem aceder à base de dados.
  
  Pode ter um máximo de 128 regras de firewall IP de nível de base de dados para uma base de dados. Para obter mais informações sobre a configuração das regras de firewall IP de nível de base de dados, consulte o exemplo mais tarde neste artigo e consulte sp_set_database_firewall_rule (Base de [Dados Azure SQL)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Recomendações sobre como definir regras de firewall

Recomendamos que utilize regras de firewall IP de nível de base de dados sempre que possível. Esta prática aumenta a segurança e torna a sua base de dados mais portátil. Utilize as regras de firewall IP do nível do servidor para administradores. Use-as também quando tem muitas bases de dados que têm os mesmos requisitos de acesso, e não pretende configurar cada base de dados individualmente.

> [!NOTE]
> Para obter informações sobre bases de dados portáteis no contexto de continuidade do negócio, veja [Authentication requirements for disa ster recovery (Requisitos de autenticação para a recuperação após desastre)](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regras de firewall IP de nível de servidor versus base de dados

*Os utilizadores de uma base de dados devem estar totalmente isolados de outra base de dados?*

Se *sim,* utilize regras de firewall IP de nível de base de dados para conceder acesso. Este método evita a utilização de regras de firewall IP ao nível do servidor, que permitem o acesso através da firewall a todas as bases de dados. Isso reduziria a profundidade das suas defesas.

*Os utilizadores nos endereços IP precisam de acesso a todas as bases de dados?*

Se *sim,* utilize as regras de firewall IP do nível do servidor para reduzir o número de vezes que tem de configurar as regras de firewall IP.

*A pessoa ou equipa que configura as regras de firewall IP só tem acesso através do portal Azure, PowerShell ou da REST API?*

Em caso afirmativo, tem de utilizar as regras de firewall IP do nível do servidor. As regras de firewall IP de nível de base de dados só podem ser configuradas através da Transact-SQL.  

*A pessoa ou equipa que configura as regras de firewall IP está proibida de ter permissão de alto nível ao nível da base de dados?*

Em caso afirmativo, utilize as regras de firewall IP de nível de servidor. Precisa de pelo menos *permissão* de BASE DE DADOS DE CONTROLO ao nível da base de dados para configurar regras de firewall IP de nível de base de dados através do Transact-SQL.  

*A pessoa ou equipa que configura ou audita as regras de firewall IP gere centralmente as regras de firewall IP para muitas (talvez centenas) de bases de dados?*

Neste cenário, as melhores práticas são determinadas pelas suas necessidades e ambiente. As regras de firewall IP ao nível do servidor podem ser mais fáceis de configurar, mas a escrita pode configurar regras ao nível da base de dados. E mesmo que utilize regras de firewall IP ao nível do servidor, poderá ter de auditar as regras de firewall IP de nível de base de dados para ver se os utilizadores com permissão *CONTROL* na base de dados criam regras de firewall IP de nível de base de dados.

*Posso usar uma mistura de regras de firewall IP ao nível do servidor e de base de dados?*

Sim. Alguns utilizadores, como administradores, podem precisar de regras de firewall IP ao nível do servidor. Outros utilizadores, como os utilizadores de uma aplicação de base de dados, podem necessitar de regras de firewall IP de nível de base de dados.

### <a name="connections-from-the-internet"></a>Conexões da internet

Quando um computador tenta ligar-se ao seu servidor de base de dados a partir da internet, a firewall verifica primeiro o endereço IP originário do pedido contra as regras de firewall IP de nível de base de dados para a base de dados que a ligação solicita.

- Se o endereço estiver dentro de um intervalo especificado nas regras de firewall IP de nível de base de dados, a ligação é concedida à base de dados SQL que contém a regra.
- Se o endereço não estiver dentro de um intervalo nas regras de firewall IP de nível de base de dados, a firewall verifica as regras de firewall IP de nível de servidor. Se o endereço estiver dentro de um intervalo que esteja nas regras de firewall IP do nível do servidor, a ligação é concedida. As regras de firewall IP ao nível do servidor aplicam-se a todas as bases de dados SQL do servidor Azure SQL.  
- Se o endereço não estiver dentro de um intervalo que esteja em qualquer uma das regras de firewall IP de nível de base de dados ou de nível de servidor, o pedido de ligação falha.

> [!NOTE]
> Para aceder à Base de Dados SQL a partir do seu computador local, certifique-se de que a firewall da sua rede e computador local permitem a comunicação de saída na porta TCP 1433.

### <a name="connections-from-inside-azure"></a>Conexões de dentro do Azure

Para permitir que as aplicações hospedadas no interior do Azure se conectem ao seu servidor SQL, as ligações Azure devem ser ativadas. Quando uma aplicação do Azure tenta ligar-se ao seu servidor de base de dados, a firewall verifica que as ligações Azure são permitidas. Uma definição de firewall que tenha endereços IP de arranque e fim iguais a *0.0.0.0* indica que as ligações Azure são permitidas. Isto pode ser ligado diretamente a partir da lâmina do Portal Azure, definindo as regras do Firewall, bem como trocando os **Serviços e recursos Do Allow Azure para aceder a este servidor** para **ON** nas definições de **Firewalls e redes virtuais.** Se a ligação não for permitida, o pedido não chega ao servidor de base de dados SQL.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações do Azure, incluindo ligações a partir das subscrições de outros clientes. Se selecionar esta opção, certifique-se de que o seu login e permissões do utilizador limitam o acesso apenas aos utilizadores autorizados.

## <a name="create-and-manage-ip-firewall-rules"></a>Criar e gerir regras de firewall IP

Cria a primeira definição de firewall ao nível do servidor utilizando o [portal Azure](https://portal.azure.com/) ou programáticamente utilizando [o Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.sql) [o Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)ou um [API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)Azure REST . Cria e gere regras adicionais de firewall IP ao nível do servidor utilizando estes métodos ou Transact-SQL.

> [!IMPORTANT]
> As regras de firewall IP de nível de base de dados só podem ser criadas e geridas utilizando a Transact-SQL.

Para melhorar o desempenho, as regras de firewall IP ao nível do servidor são temporariamente colocadas em cache ao nível da base de dados. Para atualizar a cache, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Pode utilizar a Auditoria de Base de [Dados SQL](sql-database-auditing.md) para auditar alterações de firewall ao nível do servidor e da base de dados.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Utilize o portal Azure para gerir as regras de firewall IP ao nível do servidor

Para definir uma regra de firewall IP de nível de servidor no portal Azure, vá à página de visão geral para a sua base de dados Azure SQL ou o seu servidor de base de dados SQL.

> [!TIP]
> Para um tutorial, consulte [Criar um DB utilizando o portal Azure.](sql-database-single-database-get-started.md)

#### <a name="from-the-database-overview-page"></a>A partir da página de visão geral da base de dados

1. Para definir uma regra de firewall IP de nível de servidor a partir da página de visão geral da base de dados, selecione **Definir firewall** do servidor na barra de ferramentas, como a seguinte imagem mostra. 

    ![Regra de firewall IP do servidor](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

2. **Selecione Adicionar IP** do cliente na barra de ferramentas para adicionar o endereço IP do computador que está a usar e, em seguida, selecione **Guardar**. Uma regra de firewall IP de nível de servidor é criada para o seu endereço IP atual.

    ![Definir a regra de firewall IP de nível de servidor](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>A partir da página de visão geral do servidor

A página de visão geral do seu servidor abre. Mostra o nome do servidor totalmente qualificado (como *mynewserver20170403.database.windows.net)* e fornece opções para uma configuração posterior.

1. Para definir uma regra de nível de servidor a partir desta página, selecione **Firewall** do menu **Definições** no lado esquerdo.

2. **Selecione Adicionar IP** do cliente na barra de ferramentas para adicionar o endereço IP do computador que está a usar e, em seguida, selecione **Guardar**. Uma regra de firewall IP de nível de servidor é criada para o seu endereço IP atual.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Use a Transact-SQL para gerir as regras de firewall IP

| Vista de catálogo ou procedimento armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Servidor |Apresenta as atuais regras de firewall IP ao nível do servidor |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Servidor |Cria ou atualiza as regras de firewall IP ao nível do servidor |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Servidor |Remove as regras de firewall IP de nível de servidor |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Base de Dados |Apresenta as atuais regras de firewall IP de nível de base de dados |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Base de Dados |Cria ou atualiza as regras de firewall IP de nível de base de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bases de Dados |Remove regras de firewall IP de nível de base de dados |

O exemplo seguinte analisa as regras existentes, permite uma série de endereços IP no servidor *Contoso,* e elimina uma regra de firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Em seguida, adicione uma regra de firewall IP de nível de servidor.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para eliminar uma regra de firewall IP de nível de servidor, execute o *procedimento sp_delete_firewall_rule* armazenado. O exemplo seguinte elimina a regra *ContosoFirewallRule:*

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Use powerShell para gerir regras de firewall IP ao nível do servidor 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento é agora para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos nos módulos Az e AzureRm são substancialmente idênticos.

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Servidor |Devolve as regras de firewall ao nível do servidor atuais |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Servidor |Cria uma regra de firewall ao nível do servidor nova |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Servidor |Atualiza as propriedades de uma regra de firewall ao nível do servidor existente |
| [Remover-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Servidor |Remove as regras de firewall ao nível do servidor |

O exemplo que se segue utiliza o PowerShell para definir uma regra de firewall IP de nível de servidor:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> Para $servername especificar o nome do servidor e não o nome DNS totalmente qualificado, por exemplo, especifique **o mysqldbserver** em vez de **mysqldbserver.database.windows.net**

> [!TIP]
> Para exemplos powerShell no contexto de um arranque rápido, consulte [Create DB - PowerShell](sql-database-powershell-samples.md) e Crie uma única base de dados e configure uma regra de firewall IP de nível de servidor de base [de dados SQL utilizando powerShell](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Utilize o CLI para gerir as regras de firewall IP ao nível do servidor

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
|[az sql servidor firewall-rule criar](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Servidor|Cria uma regra de firewall IP do servidor|
|[az sql lista de firewall-rule do servidor az sql](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Servidor|Lista as regras de firewall IP num servidor|
|[az sql servidor firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Servidor|Mostra o detalhe de uma regra de firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Servidor|Atualiza uma regra de firewall IP|
|[az sql servidor firewall-rule excluir](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Servidor|Elimina uma regra de firewall IP|

O exemplo seguinte utiliza o CLI para definir uma regra de firewall IP ao nível do servidor:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> Para $servername especificar o nome do servidor e não o nome DNS totalmente qualificado, por exemplo, especifique **o mysqldbserver** em vez de **mysqldbserver.database.windows.net**

> [!TIP]
> Para um exemplo cli no contexto de um arranque rápido, consulte [Create DB - Azure CLI](sql-database-cli-samples.md) e Crie uma única base de dados e configure uma regra de firewall IP de base de [dados SQL utilizando o Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Use um API REST para gerir as regras de firewall IP ao nível do servidor

| API | Nível | Descrição |
| --- | --- | --- |
| [Lista rindo regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Servidor |Apresenta as atuais regras de firewall IP ao nível do servidor |
| [Criar ou atualizar regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Servidor |Cria ou atualiza as regras de firewall IP ao nível do servidor |
| [Eliminar regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Servidor |Remove as regras de firewall IP de nível de servidor |
| [Obter regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Servidor | Obtém regras de firewall IP ao nível do servidor |

## <a name="troubleshoot-the-database-firewall"></a>Resolução de problemas na firewall da base de dados

Considere os seguintes pontos quando o acesso ao serviço de base de dados SQL não se comportar como espera.

- **Configuração local de firewall:**

  Antes de o seu computador poder aceder à Base de Dados SQL, poderá ter de criar uma exceção de firewall no seu computador para a porta TCP 1433. Para fazer ligações dentro do limite da nuvem Azure, poderá ter de abrir portas adicionais. Para mais informações, consulte a secção "SQL Database: Outside vs inside" das Portas para além de [1433 para ADO.NET base de dados 4.5 e SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Tradução de endereços de rede:**

  Devido à tradução de endereços de rede (NAT), o endereço IP utilizado pelo seu computador para ligar à Base de Dados SQL pode ser diferente do endereço IP nas definições de configuração IP do computador. Para ver o endereço IP que o seu computador está a usar para ligar ao Azure:
    1. Inicie sessão no portal.
    1. Vá ao **separador Configure** no servidor que acolhe a sua base de dados.
    1. O **endereço IP do cliente atual** é apresentado na secção **endereços IP permitidos.** Selecione **Adicionar** para **endereços IP permitidos** para permitir que este computador aceda ao servidor.

- **As alterações à lista de autorizações ainda não produziram efeito:**

  Pode haver até cinco minutos de atraso para que as alterações na configuração da firewall da Base de Dados SQL entrem em vigor.

- **O login não está autorizado, ou foi utilizada uma senha incorreta:**

  Se um login não tiver permissões no servidor de base de dados SQL ou se a palavra-passe estiver errada, a ligação ao servidor é negada. A criação de uma definição de firewall só dá aos clientes a *oportunidade* de tentarem ligar-se ao seu servidor. O cliente deve ainda fornecer as credenciais de segurança necessárias. Para obter mais informações sobre a preparação de logins, consulte Controlar e conceder acesso à base de [dados SQL e ao SQL Data Warehouse](sql-database-manage-logins.md).

- **Endereço IP dinâmico:**

  Se tiver uma ligação à Internet que utilize endereços IP dinâmicos e tiver dificuldade em passar pela firewall, experimente uma das seguintes soluções:
  
  - Peça ao seu fornecedor de serviços de internet a gama de endereços IP que é atribuída aos seus computadores clientes que acedam ao servidor de base de dados SQL. Adicione o intervalo de endereçoIP como uma regra de firewall IP.
  - Obtenha endereço IP estático em vez dos seus computadores clientes. Adicione os endereços IP como regras de firewall IP.

## <a name="next-steps"></a>Passos seguintes

- Confirme que o seu ambiente de rede corporativa permite a comunicação de entrada a partir das gamas de endereços IP computacionais (incluindo gamas SQL) que são utilizadas pelos datacenters Azure. Pode ter de adicionar esses endereços IP à lista de autorizações. Consulte as gamas IP do centro de [dados Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).  
- Para um início rápido sobre a criação de uma regra de firewall IP ao nível do servidor, consulte Criar uma base de [dados Azure SQL](sql-database-single-database-get-started.md).
- Para obter ajuda na ligação a uma base de dados Azure SQL a partir de aplicações de código aberto ou de terceiros, consulte amostras de código de arranque rápido do Cliente para a Base de [Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para obter informações sobre portas adicionais que poderá necessitar de abrir, consulte a secção "SQL Database: Outside vs inside" das Portas para além de [1433 para ADO.NET base de dados 4.5 e SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Para obter uma visão geral da segurança da Base de Dados Azure SQL, consulte [A segurança da sua base](sql-database-security-overview.md)de dados .

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

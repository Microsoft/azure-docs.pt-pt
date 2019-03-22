---
title: Regras de firewall do IP de armazém de dados e base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como configurar uma firewall de armazém de dados SQL ou base de dados SQL com regras de firewall do IP ao nível do servidor para gerir o acesso e configurar uma base de dados individual ou agrupada com regras de firewall do IP de nível de base de dados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: f2ea9f784064a926a391ba0eadebd9fa5224a36d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57875191"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Regras de firewall de base de dados SQL e o IP de armazém de dados SQL do Azure

Microsoft Azure [base de dados SQL](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) fornecer um serviço de base de dados relacional para o Azure e outros aplicativos baseados na Internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

> [!NOTE]
> Este artigo aplica-se ao servidor SQL do Azure e a base de dados SQL e SQL Data Warehouse bases de dados que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.
> [!IMPORTANT]
> Este artigo faz *não* aplicam-se ao **instância gerida da base de dados SQL do Azure**. Veja o seguinte artigo sobre [ligar a uma instância gerida](sql-database-managed-instance-connect-app.md) para obter mais informações sobre a configuração de rede necessária.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Regras de rede virtual como alternativas às regras IP

Para além das regras IP, o firewall também gerencia *regras de rede virtual*. Regras de rede virtual se baseiam nos pontos finais de serviço de rede Virtual. Regras de rede virtual podem ser preferíveis para regras IP em alguns casos. Para obter mais informações, consulte [pontos finais de serviço de rede Virtual e regras para a base de dados do Azure SQL](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Descrição geral

Inicialmente, todos os acessos ao seu servidor SQL do Azure é bloqueado pela firewall da base de dados SQL. Para aceder a um servidor de base de dados, tem de especificar uma ou mais regras do firewall IP ao nível do servidor que permitem o acesso ao seu servidor SQL do Azure. Utilize as regras de firewall do IP para especificar os intervalos de endereços IP da Internet permitidos e se as aplicações do Azure podem tentar ligar ao seu servidor SQL do Azure.

Para conceder acesso seletivamente a apenas uma das bases de dados no seu servidor do SQL Azure, tem de criar uma regra ao nível da base de dados para essa base de dados. Especifique um intervalo de endereços IP para a regra de firewall do IP de base de dados que está além do intervalo de endereços IP especificado na regra de firewall do IP ao nível do servidor e certifique-se de que o endereço IP do cliente estiver no intervalo especificado na regra ao nível da base de dados.

> [!IMPORTANT]
> Apenas o SQL Data Warehouse suporta regras de firewall do IP ao nível do servidor e não suporta regras de firewall do IP de nível de base de dados.

As tentativas de ligação a partir da Internet e do Azure têm, primeiro, de passar pela firewall antes de chegarem ao servidor do SQL Azure ou à base de dados SQL, conforme mostrado no diagrama seguinte:

   ![Diagrama com descrição da configuração da firewall.][1]

- **Regras de firewall do IP ao nível do servidor:**

  Estas regras permitem que os clientes acessar o servidor SQL do Azure completo, ou seja, todas as bases de dados no mesmo servidor de base de dados SQL. Estas regras são armazenadas na base de dados **mestra**. Regras de firewall do IP ao nível do servidor podem ser configuradas com o portal ou através de instruções de Transact-SQL. Para criar regras de firewall ao nível do servidor IP com o portal do Azure ou o PowerShell, tem de ser o proprietário da subscrição ou um contribuinte da subscrição. Para criar uma regra de firewall do IP de ao nível do servidor com o Transact-SQL, tem de ligar à instância de base de dados SQL como o início de sessão principal ao nível do servidor ou o administrador do Azure Active Directory (que significa que uma regra de firewall do IP ao nível do servidor tem de ser criada pela primeira vez por um utilizador com permissões ao nível do Azure).

- **Regras de firewall do IP de nível de base de dados:**

  Estas regras permitem que os clientes acedam a determinadas bases de dados (seguras) no mesmo servidor de base de dados SQL. Pode criar estas regras para cada base de dados (incluindo o **mestre** base de dados) e são armazenados nas bases de dados individuais. Regras de firewall do IP de nível de base de dados para bases de dados mestra e o utilizador só podem ser criadas e geridas através de instruções de Transact-SQL e apenas depois de ter configurado a primeira firewall ao nível do servidor. Se especificar um intervalo de endereços IP na regra de firewall IP ao nível da base de dados que está fora do intervalo especificado na regra de firewall do IP ao nível do servidor, apenas os clientes que tiverem endereços IP no intervalo ao nível da base de dados podem aceder a base de dados. Pode ter um máximo de 128 regras de firewall IP de nível de base de dados para uma base de dados. Para obter mais informações sobre como configurar regras de firewall do IP de nível de base de dados, consulte o exemplo mais à frente neste artigo e ver [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Recomendação

A Microsoft recomenda utilizar regras de firewall para IP de nível de base de dados sempre que possível para melhorar a segurança e tornar a sua base de dados mais portátil. Utilize as regras de firewall do IP ao nível do servidor para os administradores e se tiver muitas bases de dados que têm os mesmos requisitos de acesso e não quer perder tempo a configurar individualmente a cada base de dados.

> [!IMPORTANT]
> Base de dados do Windows Azure SQL suporta um máximo de 128 regras de firewall do IP.
> [!NOTE]
> Para obter informações sobre bases de dados portáteis no contexto de continuidade do negócio, veja [Authentication requirements for disa ster recovery (Requisitos de autenticação para a recuperação após desastre)](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Ligar a partir da Internet

Quando um computador tenta ligar ao seu servidor de base de dados a partir da Internet, a firewall verifica primeiro o endereço IP de origem do pedido com as regras de firewall IP de nível de base de dados, para a base de dados que a ligação está a pedir:

- Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall do IP do nível de base de dados, é concedida a ligação à base de dados SQL que contém a regra.
- Se o endereço IP do pedido não estiver dentro dos intervalos especificados na regra de firewall do IP ao nível da base de dados, são verificadas as regras de firewall do IP ao nível do servidor. Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras da firewall IP ao nível do servidor, é concedida ligação. Regras de firewall do IP ao nível do servidor aplicam-se a todas as bases de dados SQL no servidor SQL do Azure.  
- Se o endereço IP do pedido não estiver dentro dos intervalos especificados em qualquer nível da base de dados ou regras de firewall do IP ao nível do servidor, o pedido de ligação falha.

> [!NOTE]
> Para aceder à Base de Dados SQL do Azure a partir do seu computador local, confirme que a firewall na sua rede e no seu computador local permite comunicação de saída na porta TCP 1433.

### <a name="connecting-from-azure"></a>Ligar a partir do Azure

Para permitir que as aplicações do Azure se liguem ao servidor do SQL Azure, as ligações do Azure têm de estar ativadas. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Uma definição de firewall com inicial e final endereço igual a 0.0.0.0 indica que são permitidas ligações do Azure. Se a tentativa de ligação não for permitida, o pedido não chega ao servidor da Base de Dados SQL do Azure.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.

## <a name="creating-and-managing-ip-firewall-rules"></a>Criar e gerir regras de firewall do IP

A primeira definição de firewall ao nível do servidor pode ser criada com o [portal do Azure](https://portal.azure.com/) ou através de programação utilizando [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [da CLI do Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), ou o [ REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). As regras de firewall IP ao nível do servidor de subsequentes podem ser criadas e geridas com estes métodos e através de Transact-SQL.

> [!IMPORTANT]
> Regras de firewall do IP de nível de base de dados só podem ser criadas e geridos com o Transact-SQL.

Para melhorar o desempenho, as regras de firewall do IP ao nível do servidor são colocadas em cache temporariamente ao nível da base de dados. Para atualizar a cache, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Pode usar [a auditoria de base de dados SQL](sql-database-auditing.md) para auditar alterações de firewall ao nível do servidor e ao nível da base de dados.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Gerir regras de firewall ao nível do servidor IP com o portal do Azure

Para definir uma regra de firewall do IP ao nível do servidor no portal do Azure, pode optar por aceder à página de descrição geral para a base de dados SQL do Azure ou a página de descrição geral do seu servidor de base de dados SQL.

> [!TIP]
> Para obter um tutorial, veja [criar uma BD com o portal do Azure](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Na página de descrição geral da base de dados

1. Para definir uma regra de firewall do IP ao nível do servidor a partir da página de descrição geral da base de dados, clique em **definir firewall do servidor** na barra de ferramentas, conforme mostrado na imagem seguinte: É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

      ![regra de firewall do IP de servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Clique em **Adicionar IP de cliente** na barra de ferramentas para adicionar o endereço IP do computador que estiver a utilizar e, em seguida, clique em **guardar**. Uma regra de firewall do IP ao nível do servidor é criada para o seu endereço IP atual.

      ![Definir regra de firewall do IP ao nível do servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Na página de descrição geral do servidor

Abre a página de descrição geral do seu servidor, que mostra o nome de servidor completamente qualificado (como **mynewserver20170403.database.windows.net**) e oferece opções para configuração adicional.

1. Para definir uma regra ao nível do servidor a partir da página de descrição geral do servidor, clique em **Firewall** no menu da esquerda em definições:

2. Clique em **Adicionar IP de cliente** na barra de ferramentas para adicionar o endereço IP do computador que estiver a utilizar e, em seguida, clique em **guardar**. Uma regra de firewall do IP ao nível do servidor é criada para o seu endereço IP atual.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Gerir regras de firewall do IP com o Transact-SQL

| Vista de Catálogo ou Procedimento Armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Servidor |Apresenta as regras de firewall IP ao nível do servidor atuais |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Servidor |Cria ou atualiza as regras de firewall do IP ao nível do servidor |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Servidor |Remove as regras de firewall do IP ao nível do servidor |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Base de Dados |Apresenta as regras de firewall IP de nível de base de dados atuais |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Base de Dados |Cria ou atualiza as regras de firewall do IP de nível de base de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bases de Dados |Remove as regras de firewall ao nível da base de dados IP |

Os exemplos seguintes revisar as regras existentes, ativar um intervalo de endereços IP no servidor do Contoso e elimina uma regra de firewall do IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Em seguida, adicione uma regra de firewall do IP ao nível do servidor.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para eliminar uma regra de firewall do IP ao nível do servidor, execute o procedimento armazenado de sp_delete_firewall_rule. O exemplo seguinte elimina a regra com o nome ContosoFirewallRule:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Gerir regras de firewall ao nível do servidor IP com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Servidor |Devolve as regras de firewall ao nível do servidor atuais |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Servidor |Cria uma regra de firewall ao nível do servidor nova |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Servidor |Atualiza as propriedades de uma regra de firewall ao nível do servidor existente |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Servidor |Remove as regras de firewall ao nível do servidor |

O exemplo seguinte define uma regra de firewall do IP de ao nível do servidor com o PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Para exemplos do PowerShell no contexto de um início rápido, consulte [criar BD - PowerShell](sql-database-powershell-samples.md) e [criar uma base de dados e configurar uma regra de firewall do IP do banco de dados do SQL ao nível do servidor com o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Gerir regras de firewall ao nível do servidor IP com a CLI do Azure

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Servidor|Cria uma regra de firewall do IP de servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Servidor|Lista as regras de firewall do IP num servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Servidor|Mostra os detalhes de uma regra de firewall do IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Servidor|Atualiza uma regra de firewall do IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Servidor|Elimina uma regra de firewall do IP|

O exemplo seguinte define uma regra de firewall ao nível do servidor IP com a CLI do Azure:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Para obter um exemplo da CLI do Azure no contexto de um início rápido, consulte [criar BD - CLI do Azure](sql-database-cli-samples.md) e [criar uma base de dados e configurar uma regra de firewall do IP de base de dados SQL com a CLI do Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Gerir regras de firewall ao nível do servidor IP com a REST API

| API | Nível | Descrição |
| --- | --- | --- |
| [Listar Regras de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Servidor |Apresenta as regras de firewall IP ao nível do servidor atuais |
| [Criar ou Atualizar Regra de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Servidor |Cria ou atualiza as regras de firewall do IP ao nível do servidor |
| [Eliminar Regra de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Servidor |Remove as regras de firewall do IP ao nível do servidor |
| [Obter regras de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Servidor | Obtém as regras de firewall do IP ao nível do servidor |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Ao nível do servidor em comparação com as regras de firewall do IP de nível de base de dados

P. Os usuários de uma base de dados devem ser totalmente isolados da outra base de dados?
Se Sim, conceda acesso ao utilizar regras de firewall do IP de nível de base de dados. Isso evita com regras de firewall IP de ao nível do servidor, que permitem o acesso através da firewall para todos os bancos de dados, reduzindo a profundidade das suas defesas.

P. Os utilizadores para o endereço de IP precisam acesso a todas as bases de dados?
Utilize regras de firewall do IP ao nível do servidor para reduzir o número de vezes que tem de configurar regras de firewall do IP.

P. A pessoa ou equipa configurando as regras de firewall do IP apenas tem acesso através do portal do Azure, PowerShell ou a API REST?
Tem de utilizar as regras de firewall do IP ao nível do servidor. Regras de firewall do IP de nível de base de dados só podem ser configuradas com o Transact-SQL.  

P. É a pessoa ou equipa configurando as regras de firewall do IP pode ter permissão de alto nível ao nível da base de dados?
Utilize as regras de firewall do IP ao nível do servidor. Configurar regras de firewall do IP do nível de base de dados com o Transact-SQL, necessita de pelo menos `CONTROL DATABASE` permissão ao nível da base de dados.  

P. É a pessoa ou equipa, configuração ou as regras de firewall do IP, a auditoria gerir centralmente as regras de firewall do IP para muitos (talvez 100) de bases de dados?
Esta seleção depende das suas necessidades e ambiente. Regras de firewall do IP ao nível do servidor podem ser mais fácil de configurar, mas o script pode configurar regras ao nível da base de dados. E mesmo que use regras de firewall do IP ao nível do servidor, poderá ter de auditar as regras de firewall IP de nível de base de dados, para ver se os utilizadores com `CONTROL` permissão na base de dados tenha criado as regras de firewall do IP de nível de base de dados.

P. Pode utilizar uma combinação de regras de firewall IP ao nível do servidor e ao nível da base de dados?
Sim. Alguns usuários, como os administradores poderão ter regras de firewall do IP ao nível do servidor. Outros utilizadores, tais como utilizadores de uma aplicação de base de dados, poderão precisar de nível base de dados-regras de firewall do IP.

## <a name="troubleshooting-the-database-firewall"></a>Resolver problemas da firewall de base de dados

Se o comportamento do acesso ao serviço Base de Dados SQL do Microsoft Azure não for o esperado, considere o seguinte:

- **Configuração de local firewall:**

  Antes do computador pode aceder a SQL Database do Azure, terá de criar uma exceção de firewall no seu computador para a porta TCP 1433. Se estiver a fazer ligações dentro do limite da cloud do Azure, poderá ter de abrir portas adicionais. Para obter mais informações, consulte o **base de dados SQL: Fora do vs dentro** secção do [portas para além do 1433 para ADO.NET 4.5 e base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Tradução de endereços de rede (NAT):**

  Devido a NAT, o endereço IP utilizado pelo seu computador para ligar à base de dados do Azure SQL pode ser diferente do endereço IP mostrado nas definições de configuração de IP do computador. Para ver o endereço IP que o computador está a utilizar para ligar ao Azure, inicie sessão no portal e navegue para o separador **Configurar** no servidor que aloja a base de dados. Na secção **Endereços IP permitidos**, é apresentado o **Endereço IP de Cliente Atual**. Clique em **Adicionar** aos **endereços IP Permitidos** para permitir que este computador aceda ao servidor.

- **Alterações à lista de permissões não foram ainda aplicadas:**

  Pode ser tanto quanto o atraso de cinco minutos para que as alterações à configuração de firewall da base de dados do Azure SQL entrem em vigor.

- **O início de sessão não está autorizado ou foi utilizada uma palavra-passe incorreta:**

  Se um início de sessão não tem permissões no servidor de base de dados do Azure SQL ou a palavra-passe utilizada estiver incorreta, a ligação ao servidor de base de dados do Azure SQL é negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias. Para obter mais informações sobre a preparação dos inícios de sessão, consulte [gerir bases de dados, inícios de sessão e utilizadores na base de dados do Azure SQL](sql-database-manage-logins.md).

- **Endereço IP dinâmico:**

  Se tiver uma ligação à Internet com endereçamento IP dinâmico e estiver a ter dificuldades em passar a firewall, pode experimentar uma das seguintes soluções:
  
  - Peça ao seu fornecedor de serviços de Internet (ISP) para o intervalo de endereços IP atribuído a computadores cliente que aceder ao servidor de base de dados do Azure SQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall do IP.
  - Obtenha o endereçamento IP estático em vez disso, para os computadores cliente e, em seguida, adicione os endereços IP como regras de firewall do IP.

## <a name="next-steps"></a>Passos Seguintes

- Para um início rápido sobre como criar uma regra de firewall do IP ao nível do servidor, consulte [criar uma base de dados SQL do Azure](sql-database-single-database-get-started.md).
- Para obter ajuda para ligar a uma base de dados SQL do Azure a partir de aplicações de código aberto ou de terceiros, veja [Client quick-start code samples to SQL Database (Exemplos de código de início rápido de cliente para a Base de Dados SQL)](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para obter informações sobre portas adicionais que poderá ter de abrir, consulte o **base de dados SQL: Fora do vs dentro** secção do [portas para além do 1433 para ADO.NET 4.5 e base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Para uma descrição geral de segurança da base de dados do Azure SQL, consulte [proteger a sua base de dados](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

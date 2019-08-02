---
title: Regras de firewall de IP e do banco data warehouse de dados SQL do Azure | Microsoft Docs
description: Saiba como configurar um banco de dados SQL ou SQL Data Warehouse firewall com regras de firewall de IP no nível de servidor para gerenciar o acesso e configurar um banco de dados único ou em pool com regras de firewall de IP de nível de banco de dados.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 39d2dae28bde8ff35408733a1af886c302ec79bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568180"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Regras de firewall de IP e SQL Data Warehouse do banco de dados SQL do Azure

Microsoft Azure [banco de dados SQL](sql-database-technical-overview.md) e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) fornecem um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

> [!NOTE]
> Este artigo aplica-se ao SQL Server do Azure e ao banco de dados SQL e SQL Data Warehouse bancos que são criados no SQL Server do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.
> [!IMPORTANT]
> Este artigo não *se aplica* a **instância gerenciada do banco de dados SQL do Azure**. Consulte o seguinte artigo sobre como [se conectar a um instância gerenciada](sql-database-managed-instance-connect-app.md) para obter mais informações sobre a configuração de rede necessária.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Regras de rede virtual como alternativas às regras de IP

Além das regras de IP, o firewall também gerencia *as regras de rede virtual*. As regras de rede virtual são baseadas em pontos de extremidade de serviço de rede virtual. Regras de rede virtual podem ser preferíveis a regras de IP em alguns casos. Para saber mais, consulte [pontos de extremidade de serviço de rede virtual e regras para o banco de dados SQL do Azure](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Descrição geral

Inicialmente, todo o acesso ao seu Azure SQL Server é bloqueado pelo firewall do banco de dados SQL. Para acessar um servidor de banco de dados, você deve especificar uma ou mais regras de firewall de IP no nível de servidor que habilitam o acesso ao servidor SQL do Azure. Use as regras de firewall de IP para especificar quais intervalos de endereços IP da Internet são permitidos e se os aplicativos do Azure podem tentar se conectar ao seu SQL Server do Azure.

Para conceder acesso seletivamente a apenas uma das bases de dados no seu servidor do SQL Azure, tem de criar uma regra ao nível da base de dados para essa base de dados. Especifique um intervalo de endereços IP para a regra de firewall de IP do banco de dados que está além do intervalo de endereços IP especificado na regra de firewall de IP de nível de servidor e verifique se o endereço IP do cliente está no intervalo especificado na regra de nível de banco de dados.

> [!IMPORTANT]
> SQL Data Warehouse só dá suporte a regras de firewall de IP no nível de servidor e não oferece suporte a regras de firewall de IP no nível de banco de dados.

As tentativas de ligação a partir da Internet e do Azure têm, primeiro, de passar pela firewall antes de chegarem ao servidor do SQL Azure ou à base de dados SQL, conforme mostrado no diagrama seguinte:

   ![Diagrama com descrição da configuração da firewall.][1]

- **Regras de firewall de IP no nível de servidor:**

  Essas regras permitem que os clientes acessem todo o SQL Server do Azure, ou seja, todos os bancos de dados dentro do mesmo servidor do SQL Database. Estas regras são armazenadas na base de dados **mestra**. As regras de firewall de IP de nível de servidor podem ser configuradas usando o portal ou usando instruções Transact-SQL. Para criar regras de firewall de IP no nível de servidor usando o portal do Azure ou o PowerShell, você deve ser o proprietário da assinatura ou um colaborador de assinatura. Para criar uma regra de firewall de IP no nível de servidor usando o Transact-SQL, você deve se conectar à instância do banco de dados SQL como o logon da entidade de segurança no nível de servidor ou o administrador de Azure Active Directory (o que significa que uma regra de firewall de IP de nível de servidor deve ser criada primeiro por um usuário com permissões de nível do Azure).

- **Regras de firewall de IP no nível de banco de dados:**

  Essas regras permitem que os clientes acessem determinados bancos de dados (seguros) no mesmo servidor de banco de dado SQL. Você pode criar essas regras para cada banco de dados (incluindo o banco de dados **mestre** ) e eles são armazenados nos bancos de dados individuais. As regras de firewall de IP de nível de banco de dados para bancos de dados mestre e de usuário só podem ser criadas e gerenciadas usando instruções Transact-SQL e somente depois de ter configurado o primeiro firewall no nível de servidor. Se você especificar um intervalo de endereços IP na regra de firewall de IP de nível de banco de dados que está fora do intervalo especificado na regra de firewall de IP no nível de servidor, somente os clientes que têm endereços IP no intervalo no nível de banco de dados poderão acessar o banco de dados. Você pode ter um máximo de 128 regras de firewall de IP no nível de banco de dados para um banco de dados. Para obter mais informações sobre como configurar regras de firewall de IP no nível de banco de dados, consulte o exemplo mais adiante neste artigo e consulte [sp_set_database_firewall_rule (banco de dados SQL do Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Recomendação

A Microsoft recomenda usar regras de firewall de IP no nível de banco de dados sempre que possível para aumentar a segurança e tornar seu banco de dados mais portátil. Use regras de firewall de IP de nível de servidor para administradores e quando você tem muitos bancos de dados que têm os mesmos requisitos de acesso e não quer gastar tempo configurando cada banco de dados individualmente.

> [!IMPORTANT]
> O banco de dados SQL do Windows Azure dá suporte a um máximo de 128 regras de firewall IP.
> [!NOTE]
> Para obter informações sobre bases de dados portáteis no contexto de continuidade do negócio, veja [Authentication requirements for disa ster recovery (Requisitos de autenticação para a recuperação após desastre)](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Ligar a partir da Internet

Quando um computador tenta se conectar ao seu servidor de banco de dados pela Internet, o firewall primeiro verifica o endereço IP de origem da solicitação em relação às regras de firewall de IP no nível de banco de dados, para o banco de dados que a conexão está solicitando:

- Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall de IP no nível de banco de dados, a conexão será concedida ao banco de dados SQL que contém a regra.
- Se o endereço IP da solicitação não estiver dentro de um dos intervalos especificados na regra de firewall de IP no nível de banco de dados, as regras de firewall de IP de nível de servidor serão verificadas. Se o endereço IP da solicitação estiver dentro de um dos intervalos especificados nas regras de firewall de IP no nível de servidor, a conexão será concedida. As regras de firewall de IP de nível de servidor se aplicam a todos os bancos de dados SQL no SQL Server do Azure.  
- Se o endereço IP da solicitação não estiver dentro dos intervalos especificados em qualquer uma das regras de firewall de IP no nível de banco de dados ou de servidor, a solicitação de conexão falhará.

> [!NOTE]
> Para aceder à Base de Dados SQL do Azure a partir do seu computador local, confirme que a firewall na sua rede e no seu computador local permite comunicação de saída na porta TCP 1433.

### <a name="connecting-from-azure"></a>Ligar a partir do Azure

Para permitir que as aplicações do Azure se liguem ao servidor do SQL Azure, as ligações do Azure têm de estar ativadas. Quando uma aplicação do Azure tenta ligar ao servidor de base de dados, a firewall verifica se as ligações do Azure são permitidas. Uma configuração de firewall com endereço inicial e final igual a 0.0.0.0 indica que as conexões do Azure são permitidas. Se a tentativa de ligação não for permitida, o pedido não chega ao servidor da Base de Dados SQL do Azure.

> [!IMPORTANT]
> Esta opção configura a firewall para permitir todas as ligações a partir do Azure, incluindo ligações de subscrições de outros clientes. Quando seleciona esta opção, certifique-se de que as permissões de início de sessão e de utilizador limitam o acesso a utilizadores autorizados apenas.

## <a name="creating-and-managing-ip-firewall-rules"></a>Criando e gerenciando regras de firewall de IP

A primeira configuração de firewall no nível de servidor pode ser criada usando o [portal do Azure](https://portal.azure.com/) ou programaticamente usando [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [CLI do Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)ou a [API REST](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Regras de firewall de IP de nível de servidor subsequentes podem ser criadas e gerenciadas usando esses métodos e por meio do Transact-SQL.

> [!IMPORTANT]
> As regras de firewall de IP de nível de banco de dados só podem ser criadas e gerenciadas usando o Transact-SQL.

Para melhorar o desempenho, as regras de firewall de IP de nível de servidor são armazenadas em cache temporariamente no nível do banco de dados. Para atualizar a cache, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Você pode usar a [auditoria do banco de dados SQL](sql-database-auditing.md) para auditar alterações de firewall no nível de servidor e de banco de dados.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Gerenciar regras de firewall de IP no nível de servidor usando o portal do Azure

Para definir uma regra de firewall de IP no nível de servidor no portal do Azure, você pode ir para a página Visão geral do banco de dados SQL do Azure ou a página de visão geral do servidor do banco de dados SQL.

> [!TIP]
> Para obter um tutorial, consulte [criar um banco de uma usando o portal do Azure](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Da página Visão geral do banco de dados

1. Para definir uma regra de firewall de IP no nível de servidor na página Visão geral do banco de dados, clique em **definir Firewall do servidor** na barra de ferramentas, conforme mostrado na imagem a seguir: É aberta a página **Definições da firewall** do servidor da Base de Dados SQL.

      ![regra de firewall de IP do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando no momento e, em seguida, clique em **salvar**. Uma regra de firewall de IP no nível de servidor é criada para seu endereço IP atual.

      ![definir regra de firewall de IP de nível de servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Página Visão geral do servidor

A página Visão geral do seu servidor é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170403.Database.Windows.net**) e fornece opções para configuração adicional.

1. Para definir uma regra de nível de servidor na página Visão geral do servidor, clique em **Firewall** no menu à esquerda em configurações:

2. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando no momento e, em seguida, clique em **salvar**. Uma regra de firewall de IP no nível de servidor é criada para seu endereço IP atual.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Gerenciar regras de firewall de IP usando Transact-SQL

| Vista de Catálogo ou Procedimento Armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Servidor |Exibe as regras atuais de firewall de IP no nível de servidor |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Servidor |Criar ou atualizar regras de firewall de IP no nível de servidor |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Servidor |Remove regras de firewall de IP de nível de servidor |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Base de Dados |Exibe as regras atuais de firewall de IP no nível de banco de dados |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Base de Dados |Cria ou atualiza as regras de firewall de IP no nível de banco de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bases de Dados |Remove regras de firewall de IP de nível de banco de dados |

Os exemplos a seguir revisam as regras existentes, habilitam um intervalo de endereços IP no servidor Contoso e exclui uma regra de firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Em seguida, adicione uma regra de firewall de IP de nível de servidor.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para excluir uma regra de firewall de IP no nível de servidor, execute o procedimento armazenado sp_delete_firewall_rule. O exemplo a seguir exclui a regra chamada ContosoFirewallRule:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Gerenciar regras de firewall de IP no nível de servidor usando Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Servidor |Devolve as regras de firewall ao nível do servidor atuais |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Servidor |Cria uma regra de firewall ao nível do servidor nova |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Servidor |Atualiza as propriedades de uma regra de firewall ao nível do servidor existente |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Servidor |Remove as regras de firewall ao nível do servidor |

O exemplo a seguir define uma regra de firewall de IP no nível de servidor usando o PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Para obter exemplos do PowerShell no contexto de um início rápido, consulte [criar DB-PowerShell](sql-database-powershell-samples.md) e [criar um banco de dados individual e configurar uma regra de firewall de IP de nível de servidor do banco de dados SQL usando o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Gerenciar regras de firewall de IP no nível de servidor usando CLI do Azure

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Servidor|Cria uma regra de firewall de IP do servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Servidor|Lista as regras de firewall IP em um servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Servidor|Mostra o detalhe de uma regra de firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Servidor|Atualiza uma regra de firewall de IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Servidor|Exclui uma regra de firewall IP|

O exemplo a seguir define uma regra de firewall de IP no nível de servidor usando o CLI do Azure:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Para obter um exemplo de CLI do Azure no contexto de um início rápido, consulte [Create DB-CLI do Azure](sql-database-cli-samples.md) e [criar um banco de dados individual e configurar uma regra de firewall de IP do banco de dados SQL usando o CLI do Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Gerenciar regras de firewall de IP no nível de servidor usando a API REST

| API | Nível | Descrição |
| --- | --- | --- |
| [Listar Regras de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Servidor |Exibe as regras atuais de firewall de IP no nível de servidor |
| [Criar ou Atualizar Regra de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Servidor |Criar ou atualizar regras de firewall de IP no nível de servidor |
| [Eliminar Regra de Firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Servidor |Remove regras de firewall de IP de nível de servidor |
| [Obter regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Servidor | Obtém as regras de firewall de IP no nível de servidor |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regras de firewall de IP de nível de servidor versus de nível de banco de dados

P. Os usuários de um banco de dados devem estar totalmente isolados de outro banco de dados?
Em caso afirmativo, conceda acesso usando regras de firewall de IP no nível de banco de dados. Isso evita o uso de regras de firewall de IP no nível de servidor, que permitem o acesso por meio do firewall a todos os bancos de dados, reduzindo a profundidade de suas defesas.

P. Os usuários no endereço IP precisam de acesso a todos os bancos de dados?
Use regras de firewall de IP no nível de servidor para reduzir o número de vezes que você deve configurar regras de firewall IP.

P. A pessoa ou equipe que está configurando as regras de firewall IP só tem acesso por meio do portal do Azure, do PowerShell ou da API REST?
Você deve usar regras de firewall de IP no nível de servidor. As regras de firewall de IP de nível de banco de dados só podem ser configuradas usando o Transact-SQL.  

P. A pessoa ou a equipe que está configurando as regras de firewall IP proibidas de ter uma permissão de alto nível no nível do banco de dados?
Use regras de firewall de IP no nível de servidor. A configuração de regras de firewall de IP no nível de banco de dados usando `CONTROL DATABASE` o Transact-SQL requer pelo menos a permissão no nível do banco de dados.  

P. A pessoa ou a equipe está configurando ou auditando as regras de firewall de IP, gerenciando centralmente as regras de firewall de IP para muitos (talvez 100s) de bancos de dados?
Essa seleção depende de suas necessidades e do seu ambiente. As regras de firewall de IP de nível de servidor podem ser mais fáceis de configurar, mas o script pode configurar regras no nível de banco de dados. E mesmo que você use regras de firewall de IP no nível de servidor, talvez seja necessário auditar as regras de firewall de IP no nível de banco `CONTROL` de dados para ver se os usuários com permissão no banco de dados criaram regras de firewall de IP no nível de banco de dados.

P. Posso usar uma combinação de regras de firewall de IP no nível de servidor e de banco de dados?
Sim. Alguns usuários, como administradores, podem precisar de regras de firewall de IP de nível de servidor. Outros usuários, como usuários de um aplicativo de banco de dados, podem precisar de regras de firewall de IP no nível de banco de dados.

## <a name="troubleshooting-the-database-firewall"></a>Resolver problemas da firewall de base de dados

Se o comportamento do acesso ao serviço Base de Dados SQL do Microsoft Azure não for o esperado, considere o seguinte:

- **Configuração do firewall local:**

  Antes que o computador possa acessar o banco de dados SQL do Azure, talvez seja necessário criar uma exceção de firewall em seu computador para a porta TCP 1433. Se estiver a fazer ligações dentro do limite da cloud do Azure, poderá ter de abrir portas adicionais. Para obter mais informações, consulte **o banco de dados SQL: Fora do vs** Inside seção de [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **NAT (conversão de endereços de rede):**

  Devido ao NAT, o endereço IP usado pelo computador para se conectar ao banco de dados SQL do Azure pode ser diferente do endereço IP mostrado nas definições de configuração de IP do computador. Para ver o endereço IP que o computador está a utilizar para ligar ao Azure, inicie sessão no portal e navegue para o separador **Configurar** no servidor que aloja a base de dados. Na secção **Endereços IP permitidos**, é apresentado o **Endereço IP de Cliente Atual**. Clique em **Adicionar** aos **endereços IP Permitidos** para permitir que este computador aceda ao servidor.

- **As alterações na lista de permissões ainda não entraram em vigor:**

  Pode haver um atraso de até cinco minutos para que as alterações na configuração do firewall do banco de dados SQL do Azure entrem em vigor.

- **O logon não está autorizado ou uma senha incorreta foi usada:**

  Se um logon não tiver permissões no servidor do banco de dados SQL do Azure ou se a senha usada estiver incorreta, a conexão com o servidor do banco de dados SQL do Azure será negada. Criar uma definição de firewall só proporciona aos clientes uma oportunidade para tentar ligar ao seu servidor; cada cliente tem de indicar as credenciais de segurança necessárias. Para obter mais informações sobre a preparação de logons, consulte Gerenciando bancos de dados [, logons e usuários no banco de dados SQL do Azure](sql-database-manage-logins.md).

- **Endereço IP dinâmico:**

  Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver tendo problemas para passar pelo firewall, poderá tentar uma das seguintes soluções:
  
  - Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos seus computadores cliente que acessam o servidor do banco de dados SQL do Azure e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall de IP.
  - Obtenha o endereçamento IP estático em vez de seus computadores cliente e, em seguida, adicione os endereços IP como regras de firewall IP.

## <a name="next-steps"></a>Passos Seguintes

- Confirme se o seu ambiente de rede corporativa permite a comunicação de entrada dos intervalos de endereços IP de computação (incluindo intervalos SQL) usados pelos data centers Microsoft Azure. Pode ser necessário colocar esses endereços IP na lista de permissões, ver [Microsoft Azure intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) do datacenter  
- Para obter um início rápido sobre a criação de uma regra de firewall de IP no nível de servidor, consulte [criar um banco de dados SQL do Azure](sql-database-single-database-get-started.md).
- Para obter ajuda para ligar a uma base de dados SQL do Azure a partir de aplicações de código aberto ou de terceiros, veja [Client quick-start code samples to SQL Database (Exemplos de código de início rápido de cliente para a Base de Dados SQL)](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para obter informações sobre portas adicionais que talvez precisem ser abertas, consulte **o banco de dados SQL: Fora do vs** Inside seção de [portas além de 1433 para ADO.NET 4,5 e banco de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Para obter uma visão geral da segurança do banco de dados SQL do Azure, consulte [protegendo seu banco de dados](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

---
title: 'Quickstart: Criar uma piscina SQL dedicada (anteriormente SQL DW) com Azure PowerShell'
description: Crie rapidamente uma piscina SQL dedicada (anteriormente SQL DW) com uma regra de firewall ao nível do servidor utilizando a Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
ms.author: xiaoyul
manager: craigg
ms.reviewer: igorstan
ms.date: 4/11/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 65bf509c8eb654a9f7712fdf7b94ff7fa26a3d32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537455"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Quickstart: Criar uma piscina SQL dedicada (anteriormente SQL DW) com Azure PowerShell

Crie uma piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics utilizando a Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!IMPORTANT]
> A criação de uma piscina SQL dedicada (anteriormente SQL DW) pode resultar num novo serviço de faturação.  Para mais informações, consulte [os preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na sua subscrição Azure utilizando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

Para ver qual a subscrição que está a utilizar, execute [a subscrição Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Se precisar de utilizar uma subscrição diferente do padrão, executar [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Criar variáveis

Defina variáveis para utilização nos scripts neste início rápido.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Um grupo de recursos é um contentor no qual os recursos da Azure são implantados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Criar um servidor

Crie um [servidor SQL lógico](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) utilizando o comando [New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Os servidores contêm um grupo de bases de dados geridas como um grupo. O exemplo a seguir cria um servidor com nome aleatório no seu grupo de recursos com um utilizador administrativo nomeado `ServerAdmin` e uma palavra-passe de `ChangeYourAdminPassword1` . Substitua estes valores predefinidos conforme quiser.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

Crie uma [regra de firewall ao nível](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) do servidor utilizando o comando [New-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Uma regra de firewall ao nível do servidor permite que uma aplicação externa, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte a uma piscina SQL dedicada (anteriormente SQL DW) através da firewall dedicada do serviço de piscina SQL.

No exemplo seguinte, a firewall apenas é aberta para outros recursos do Azure. Para ativar a conectividade externa, altere o endereço IP para um endereço adequado para o seu ambiente. Para abrir todos os endereços IP, utilize 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Os pontos finais SQL comunicam-se sobre a porta 1433. Se estiver a tentar ligar-se a partir de uma rede corporativa, o tráfego de saída sobre a porta 1433 pode não ser permitido pela firewall da sua rede. Em caso afirmativo, não poderá ligar-se ao seu servidor a menos que o seu departamento de TI abra a porta 1433.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Crie uma piscina SQL dedicada (anteriormente SQL DW)

O exemplo a seguir cria uma piscina SQL dedicada (anteriormente SQL DW) utilizando as variáveis previamente definidas.  Especifica o objetivo de serviço como DW100c, que é um ponto de partida de baixo custo para a sua piscina DE SQL dedicada (anteriormente SQL DW).

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Os parâmetros necessários são:

* **Nome solicitado do ServiçoObjetivo**: A quantidade de unidades de armazém de [dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) que está a solicitar. Aumentar este montante aumenta o custo do cálculo. Para obter uma lista de valores suportados, consulte [os limites de memória e de concordância](memory-concurrency-limits.md).
* **Nome base de dados**: O nome da piscina SQL dedicada (anteriormente SQL DW) que está a criar.
* **Nome do servidor**: O nome do servidor que está a utilizar para criação.
* **Nome do Grupo de Recursos**: Grupo de recursos que está a utilizar. Para localizar grupos de recursos disponíveis na sua subscrição, utilize Get-AzureResource.
* **Edição**: Deve ser "DataWarehouse" para criar uma piscina SQL dedicada (anteriormente SQL DW).

Os parâmetros opcionais são:

* **CollationName**: o agrupamento predefinido quando não é especificado é SQL_Latin1_General_CP1_CI_AS. A colagem não pode ser alterada numa base de dados.
* **MaxSizeBytes**: O tamanho máximo padrão de uma base de dados é de 240TB. O tamanho máximo limita os dados da loja de linha. Há armazenamento ilimitado para dados colunar.

Para obter mais informações sobre as opções de parâmetros, consulte [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido.

> [!TIP]
> Se planeia continuar a trabalhar com tutoriais de arranque rápido mais tarde, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, use os seguintes passos para eliminar todos os recursos criados por este quickstart no portal Azure.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passos seguintes

Criou agora uma piscina SQL dedicada (anteriormente SQL DW), criou uma regra de firewall e está ligada à sua piscina SQL dedicada. Para saber mais, continue os dados de Carga num artigo [dedicado à piscina SQL.](./load-data-from-azure-blob-storage-using-copy.md)

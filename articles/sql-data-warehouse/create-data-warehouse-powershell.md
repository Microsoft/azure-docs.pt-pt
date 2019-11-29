---
title: 'Início rápido: criar um depósito-Azure PowerShell'
description: Crie rapidamente um servidor lógico do banco de dados SQL, uma regra de firewall de nível de servidor e data warehouse com Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 44abf23ce632aa1ac630eab3313ffd675eef1a0b
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559163"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Início rápido: criar e consultar uma SQL Data Warehouse do Azure com Azure PowerShell

Crie rapidamente uma SQL Data Warehouse do Azure usando Azure PowerShell.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Para obter mais informações, veja [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Entre em sua assinatura do Azure usando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Para ver qual assinatura você está usando, execute [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Se você precisar usar uma assinatura diferente da padrão, execute [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Criar variáveis

Defina variáveis para utilização nos scripts neste início rápido.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um [servidor lógico do SQL Azure](../sql-database/sql-database-logical-servers.md) usando o comando [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) . Um servidor lógico contém um grupo de bases de dados geridas como um grupo. O exemplo a seguir cria um servidor nomeado aleatoriamente em seu grupo de recursos com um usuário administrador chamado `ServerAdmin` e uma senha de `ChangeYourAdminPassword1`. Substitua estes valores predefinidos conforme quiser.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Crie uma [regra de firewall no nível de servidor do SQL do Azure](../sql-database/sql-database-firewall-configure.md) usando o comando [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) . Uma regra de firewall no nível de servidor permite que um aplicativo externo, como SQL Server Management Studio ou o utilitário SQLCMD se conecte a um SQL Data Warehouse por meio do firewall do serviço de SQL Data Warehouse. No exemplo seguinte, a firewall apenas é aberta para outros recursos do Azure. Para ativar a conectividade externa, altere o endereço IP para um endereço adequado para o seu ambiente. Para abrir todos os endereços IP, utilize 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> O banco de dados SQL e o SQL Data Warehouse se comunicam pela porta 1433. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 pode não ser permitido pelo firewall da sua rede. Nesse caso, você não poderá se conectar ao SQL Server do Azure, a menos que o departamento de ti Abra a porta 1433.
>


## <a name="create-a-data-warehouse"></a>Criar um armazém de dados
Este exemplo cria um data warehouse usando as variáveis definidas anteriormente.  Ele especifica o objetivo de serviço como DW100c, que é um ponto de partida de menor custo para sua data warehouse. 

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

* **RequestedServiceObjectiveName**: a quantidade de [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) que você está solicitando. Aumentar essa quantidade aumenta o custo de computação. Para obter uma lista de valores com suporte, consulte [limites de memória e simultaneidade](memory-concurrency-limits.md).
* **DatabaseName**: o nome do SQL data warehouse que você está criando.
* **ServerName**: o nome do servidor que você está usando para a criação.
* **ResourceGroupName**: grupo de recursos que você está usando. Para localizar grupos de recursos disponíveis na sua subscrição, utilize Get-AzureResource.
* **Edição**: tem de ser "DataWarehouse", para criar um SQL Data Warehouse.

Os parâmetros opcionais são:

- **CollationName**: o agrupamento predefinido quando não é especificado é SQL_Latin1_General_CP1_CI_AS. O agrupamento não pode ser alterado em um banco de dados.
- **MaxSizeBytes**: o tamanho máximo padrão de um banco de dados é 240TB. O tamanho máximo limita os dados de armazenagem. Há armazenamento ilimitado para dados de coluna.

Para obter mais informações sobre as opções de parâmetro, consulte [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. 

> [!TIP]
> Se você planeja continuar trabalhando com os tutoriais de início rápido posteriores, não limpe os recursos criados neste guia de início rápido. Se você não planeja continuar, use as etapas a seguir para excluir todos os recursos criados por este guia de início rápido no portal do Azure.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passos seguintes

Agora você criou um data warehouse, criou uma regra de firewall, conectou-se à sua data warehouse e executou algumas consultas. Para saber mais sobre o Azure SQL Data Warehouse, avance para o tutorial para carregar dados.
> [!div class="nextstepaction"]
>[Carregar dados em um SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)

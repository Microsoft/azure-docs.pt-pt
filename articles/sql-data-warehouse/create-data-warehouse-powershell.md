---
title: 'Início rápido: Criar um armazém de dados SQL do Azure – Powershell do Azure | Documentos da Microsoft'
description: Crie rapidamente um servidor lógico de base de dados SQL, a regra de firewall ao nível do servidor e o armazém de dados com o Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/15/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 3358c415e620165bf07e2810bc8f1873d1dea0d2
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466392"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Início rápido: Criar e consultar um armazém de dados SQL do Azure com o Azure PowerShell

Crie rapidamente um armazém de dados SQL do Azure com o Azure PowerShell.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Este tutorial requer a versão 5.1.1 ou posterior do módulo do Azure PowerShell. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão que tem atualmente. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps)(Instalar o módulo do Azure PowerShell). 


> [!NOTE]
> A criação de um SQL Data Warehouse poderá resultar num novo serviço sujeito a faturação.  Para obter mais informações, veja [Preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) de comando e siga na tela as direções.

```powershell
Add-AzureRmAccount
```

Para ver a subscrição que está a utilizar, execute [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Se precisar de utilizar uma subscrição diferente que não o predefinido, execute [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
Set-AzureRmContext -SubscriptionName "MySubscription"
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

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Criar uma [servidor lógico SQL do Azure](../sql-database/sql-database-logical-servers.md) utilizando o [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) comando. Um servidor lógico contém um grupo de bases de dados geridas como um grupo. O exemplo seguinte cria um servidor de nome aleatório no seu grupo de recursos com um utilizador de administrador com o nome `ServerAdmin` e uma palavra-passe de `ChangeYourAdminPassword1`. Substitua estes valores predefinidos conforme quiser.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Criar uma [regra de firewall ao nível do servidor SQL do Azure](../sql-database/sql-database-firewall-configure.md) utilizando o [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) comando. Uma regra de firewall ao nível do servidor permite que uma aplicação externa, como o SQL Server Management Studio ou o utilitário SQLCMD para ligar a um SQL data warehouse através da firewall de serviço do SQL Data Warehouse. No exemplo seguinte, a firewall apenas é aberta para outros recursos do Azure. Para ativar a conectividade externa, altere o endereço IP para um endereço adequado para o seu ambiente. Para abrir todos os endereços IP, utilize 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Base de dados SQL e SQL Data Warehouse comunicam através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não será possível ligar ao servidor SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Criar um armazém de dados com dados de exemplo
Este exemplo cria um armazém de dados com as variáveis definidas anteriormente.  Especifica o objetivo de serviço como DW400, que é um ponto de partida de custo mais baixo e para o seu armazém de dados. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Os parâmetros necessários são:

* **RequestedServiceObjectiveName**: A quantidade de [unidades de armazém de dados](what-is-a-data-warehouse-unit-dwu-cdwu.md) que está a solicitar. Aumentar esse valor aumenta o custo de computação. Para obter uma lista de valores suportados, consulte [limites de memória e simultaneidade](memory-and-concurrency-limits.md).
* **DatabaseName**: O nome do SQL Data Warehouse que está a criar.
* **ServerName**: O nome do servidor que está a utilizar para a criação.
* **ResourceGroupName**: Grupo de recursos que está a utilizar. Para localizar grupos de recursos disponíveis na sua subscrição, utilize Get-AzureResource.
* **Edição**: Tem de ser "DataWarehouse" para criar um SQL Data Warehouse.

Os parâmetros opcionais são:

- **CollationName**: O agrupamento predefinido se não for especificado é SQL_Latin1_General_CP1_CI_AS. Não é possível alterar o agrupamento numa base de dados.
- **MaxSizeBytes**: O tamanho máximo predefinido de uma base de dados é de 10 GB.

Para obter mais informações sobre as opções de parâmetros, consulte [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. 

> [!TIP]
> Se planeia continuar a trabalhar com os tutoriais de início rápido posteriores, não limpe os recursos criados neste início rápido. Se não quiser continuar, utilize os seguintes passos para eliminar todos os recursos criados neste início rápido no portal do Azure.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passos Seguintes

Acabou de criar um armazém de dados, criado uma regra de firewall, ligada ao seu armazém de dados e executar algumas consultas. Para saber mais sobre o Azure SQL Data Warehouse, avance para o tutorial para carregar dados.
> [!div class="nextstepaction"]
>[Carregar dados para o SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)

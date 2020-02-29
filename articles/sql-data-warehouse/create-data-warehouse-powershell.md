---
title: 'Quickstart: Criar um armazém de dados (PowerShell)'
description: Crie rapidamente um servidor lógico de armazém de dados Azure Synapse Analytics, com uma regra de firewall ao nível do servidor utilizando o Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9df9b4b1bdb33a856d9e31d65981e8654af049d2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200010"
---
# <a name="quickstart-create--query-a-data-warehouse-with-azure-powershell"></a>Quickstart: Criar e consultar um armazém de dados com a Azure PowerShell

Crie um armazém de dados Azure Synapse Analytics, disponibilizando uma piscina SQL utilizando o Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de um armazém pode resultar num novo serviço de faturação.  Para mais informações, consulte o preço da [Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se na subscrição do Azure utilizando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

Para ver que subscrição está a usar, execute a [Subscrição Get-Az](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Se precisar de utilizar uma subscrição diferente da predefinida, execute o [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

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
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de [recursos Azure](../azure-resource-manager/management/overview.md) utilizando o comando [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um [servidor lógico Azure SQL](../sql-database/sql-database-logical-servers.md) utilizando o comando [New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver) Um servidor lógico contém um grupo de bases de dados geridas como um grupo. O exemplo seguinte cria um servidor nomeado aleatoriamente no seu grupo de recursos com um utilizador administrativo chamado `ServerAdmin` e uma palavra-passe de `ChangeYourAdminPassword1`. Substitua estes valores predefinidos conforme quiser.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall do servidor

Crie uma regra de firewall de [nível de servidor Azure SQL](../sql-database/sql-database-firewall-configure.md) utilizando o comando [New-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule) Uma regra de firewall ao nível do servidor permite que uma aplicação externa, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte a um Armazém de Dados SQL através da firewall de serviço SQL Data Warehouse. No exemplo seguinte, a firewall apenas é aberta para outros recursos do Azure. Para ativar a conectividade externa, altere o endereço IP para um endereço adequado para o seu ambiente. Para abrir todos os endereços IP, utilize 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Os pontos finais SQL comunicam sobre a porta 1433. Se estiver a tentar ligar-se a partir de uma rede corporativa, o tráfego de saída sobre a porta 1433 pode não ser permitido pela firewall da sua rede. Em caso afirmativo, não poderá ligar-se ao seu servidor Azure SQL a menos que o seu departamento de TI abra a porta 1433.
>


## <a name="create-a-data-warehouse"></a>Criar um armazém de dados
Este exemplo cria um armazém de dados utilizando as variáveis previamente definidas.  Especifica o objetivo de serviço como DW100c, que é um ponto de partida de baixo custo para o seu armazém de dados. 

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

* **Serviço solicitadoNome Objetivo**: A quantidade de [unidades](what-is-a-data-warehouse-unit-dwu-cdwu.md) de armazém de dados que está a solicitar. Aumentar este montante aumenta o custo da computação. Para obter uma lista de valores suportados, consulte os limites da [memória e da moeda.](memory-concurrency-limits.md)
* **Nome base**de dados : O nome do armazém de dados que está a criar.
* **Nome**do servidor : O nome do servidor que está a usar para a criação.
* **Nome do Grupo de Recursos**: Grupo de recursos que está a utilizar. Para localizar grupos de recursos disponíveis na sua subscrição, utilize Get-AzureResource.
* **Edição**: Deve ser "DataWarehouse" para criar um armazém de dados.

Os parâmetros opcionais são:

- **CollationName**: o agrupamento predefinido quando não é especificado é SQL_Latin1_General_CP1_CI_AS. A colagem não pode ser alterada numa base de dados.
- **MaxSizeBytes**: O tamanho máximo padrão de uma base de dados é de 240TB. O tamanho máximo limita os dados da loja de linhas. Há armazenamento ilimitado para dados colunares.

Para obter mais informações sobre as opções do parâmetro, consulte [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. 

> [!TIP]
> Se pretende continuar a trabalhar com tutoriais mais rápidos, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, use os seguintes passos para eliminar todos os recursos criados por este arranque rápido no portal Azure.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passos seguintes

Criou agora um armazém de dados, criou uma regra de firewall, ligada ao seu armazém de dados, e executou algumas consultas. Para saber mais, continue ao tutorial para carregar dados.
> [!div class="nextstepaction"]
>[Carregue dados num armazém de dados](load-data-from-azure-blob-storage-using-polybase.md)

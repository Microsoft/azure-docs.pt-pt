---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: ad6e444f9672fbe521e9c6963649d250830154d8
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046139"
---
Neste passo, cria-se um [servidor SQL lógico](../database/logical-servers.md) e uma [única base de dados](../database/single-database-overview.md) que utiliza dados da amostra AdventureWorksLT. Pode criar a base de dados utilizando menus e ecrãs do portal Azure, ou utilizando um script Azure CLI ou PowerShell na Casca de Nuvem Azure.

Todos os métodos incluem a criação de uma regra de firewall ao nível do servidor para permitir o endereço IP público do computador que está a usar para aceder ao servidor. Para obter mais informações sobre a criação de regras de firewall ao nível do servidor, consulte [Criar uma firewall ao nível do servidor](../database/firewall-create-server-level-portal-quickstart.md). Também pode definir regras de firewall ao nível da base de dados. Consulte [Criar uma regra de firewall de nível de base de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar um grupo de recursos, servidor e única base de dados no portal Azure:

1. Inicie sessão no [portal](https://portal.azure.com).
1. A partir da barra de pesquisa, procure e selecione **Azure SQL**.
1. Na página **Azure SQL,** selecione **Adicionar**.

   ![Adicione ao Azure SQL](./media/sql-database-create-single-database/sqldbportal.png)

1. Na página de opção de **implementação Select SQL,** selecione o azulejo das bases de **dados SQL,** com base de **dados única** no tipo **de Recurso**. Pode ver mais informações sobre as diferentes bases de dados selecionando **detalhes do Show**.
1. Selecione **Criar**.

   ![Criar base de dados individual](./media/sql-database-create-single-database/create-single-database.png)

1. No separador **Basics** do formulário de base de **dados Create SQL,** sob **os detalhes do Projeto,** selecione a **subscrição** azure correta se ainda não estiver selecionada.
1. No **grupo Recursos**, selecione Criar **novo**, introduza *o myResourceGroup,* e selecione **OK**.
1. Em dados de base de **dados,** para **nome da Base de Dados,** insira *o mySampleDatabase*.
1. Para **o Servidor,** selecione **Criar novo**, e preencha o novo formulário do **servidor** da seguinte forma:
   - **Nome do servidor**: Introduza *o mysqlserver,* e alguns caracteres para singularidade.
   - **Login de administração do servidor**: *Introduza azureuser*.
   - **Palavra-passe**: Introduza uma palavra-passe que satisfaça os requisitos e introduza-a novamente no campo **de palavra-passe Confirmar.**
   - **Localização**: Desça e escolha um local, como **(EUA) East US**.

   Selecione **OK**.

   ![Novo servidor](./media/sql-database-create-single-database/new-server.png)

   Grave o registo do servidor e a palavra-passe para que possa iniciar sessão no servidor e nas suas bases de dados. Se esquecer o seu login ou palavra-passe, pode obter o nome de login ou redefinir a palavra-passe na página do **servidor SQL** após a criação da base de dados. Para abrir a página do **servidor SQL,** selecione o nome do servidor na página **'Overview'** da base de dados.

1. Em **cálculo + armazenamento,** se pretender reconfigurar as predefinições, selecione base de **dados Configure**.

   Na página **Configure,** pode opcionalmente:
   - Alterar o **nível Compute** de **Provisioned** para **Serverless**.
   - Reveja e altere as definições para **vCores** e **tamanho máximo de dados**.
   - Selecione **configuração De alterar** para alterar a geração de hardware.

   Depois de efazer quaisquer alterações, selecione **Aplicar**.

1. Selecione **Seguinte: Networking** na parte inferior da página.

   ![Nova base de dados SQL - Separador básico](./media/sql-database-create-single-database/new-sql-database-basics.png)

1. No separador **Networking,** sob o **método de conectividade,** selecione **ponto final público**.
1. De acordo com **as regras da Firewall,** desemperte **o endereço IP do cliente atual** para **Sim**.
1. Selecione **Seguinte: Configurações adicionais** na parte inferior da página.

   ![Separador de rede](./media/sql-database-create-single-database/networking.png)
  
   Para obter mais informações sobre as definições de firewall, consulte permitir que [os serviços e recursos do Azure acedam a este servidor](../database/network-access-controls-overview.md) e adicione um ponto final [privado](../database/private-endpoint-overview.md).

1. No separador **de definições Adicional,** na secção **Fonte de Dados,** para **utilização de dados existentes,** selecione **Sample**.
1. Selecione **Review + criar** na parte inferior da página.

   ![Separador de definições adicionais](./media/sql-database-create-single-database/additional-settings.png)

1. Depois de rever as definições, selecione **Criar**.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode criar um grupo de recursos Azure, servidor e base de dados única utilizando a interface de linha de comando Azure (Azure CLI). Se não quiser utilizar a Casca de Nuvem Azure, instale o [Azure CLI](/cli/azure/install-azure-cli) no seu computador.

Para executar a seguinte amostra de código em Azure Cloud Shell, selecione **Experimente-a** na barra de título da amostra de código. Quando a Cloud Shell abrir, selecione **Copy** na barra de título da amostra de código e cole a amostra de código na janela Cloud Shell. No código, `<Subscription ID>` substitua-o pelo seu ID de subscrição Azure e `$startip` `$endip` `0.0.0.0` substitua-o pelo endereço IP público do computador que está a utilizar.

Siga as instruções no ecrã para iniciar sessão no Azure e executar o código.

Também pode utilizar a Casca de Nuvem Azure do portal Azure, selecionando o ícone Cloud Shell na barra superior.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

A primeira vez que utilizar a Cloud Shell no portal, selecione **Bash** no diálogo **Welcome.** As sessões subsequentes usarão o Azure CLI num ambiente Bash, ou pode selecionar **Bash** a partir da barra de controlo Cloud Shell.

O seguinte código Azure CLI cria um grupo de recursos, servidor, única base de dados e firewall IP ao nível do servidor para acesso ao servidor. Certifique-se de gravar os nomes do grupo de recursos gerados e do servidor, para que possa gerir estes recursos mais tarde.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a server-level firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

O código anterior utiliza estes comandos Azure CLI:

| Comando | Descrição |
|---|---|
| [az conjunto de conta](/cli/azure/account?view=azure-cli-latest#az-account-set) | Define uma subscrição para ser a subscrição ativa atual. |
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor que acolhe bases de dados e piscinas elásticas. |
| [az sql servidor firewall-rule criar](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Cria uma regra de firewall ao nível do servidor. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Cria uma base de dados. |

Para mais amostras azure SQL Database Azure CLI, consulte [amostras Azure CLI](../database/az-cli-script-samples-content-guide.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode criar um grupo de recursos, servidor e única base de dados utilizando o Windows PowerShell. Se não quiser utilizar a Casca de Nuvem Azure, [instale o módulo Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para executar a seguinte amostra de código na Casca de Nuvem Azure, selecione **Experimente na** barra de código. Quando a Cloud Shell abrir, selecione **Copy** na barra de título da amostra de código e cole a amostra de código na janela Cloud Shell. No código, `<Subscription ID>` substitua-o pelo seu ID de subscrição Azure e `$startIp` `$endIp` `0.0.0.0` substitua-o pelo endereço IP público do computador que está a utilizar.

Siga as instruções no ecrã para iniciar sessão no Azure e executar o código.

Também pode utilizar a Azure Cloud Shell do portal Azure, selecionando o ícone Cloud Shell na barra superior.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

A primeira vez que utilizar a Cloud Shell a partir do portal, selecione **PowerShell** no diálogo **Welcome.** As sessões subsequentes utilizarão o PowerShell ou pode selecioná-la a partir da barra de controlo Cloud Shell.

O seguinte código PowerShell cria um grupo de recursos Azure, servidor, única base de dados e regra de firewall para acesso ao servidor. Certifique-se de gravar os nomes do grupo de recursos gerados e do servidor, para que possa gerir estes recursos mais tarde.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

O código anterior utiliza estes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que acolhe bases de dados e piscinas elásticas. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall ao nível do servidor para um servidor. |
| [Nova AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados. |

Para mais amostras de Base de Dados Azure SQL PowerShell, consulte [as amostras da Azure PowerShell](../database/powershell-script-content-guide.md).

---

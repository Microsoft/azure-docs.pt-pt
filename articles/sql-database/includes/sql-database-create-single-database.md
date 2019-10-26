---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 07/31/2019
ms.author: mathoma
ms.openlocfilehash: ff5505c2cb35d088565773e8d0ba01e8abb4b8c3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933246"
---
Nesta etapa, você criará um banco de dados individual do banco de dados SQL do Azure. 

> [!IMPORTANT]
> Certifique-se de configurar as regras de firewall para usar o endereço IP público do computador que você está usando para concluir este artigo.
>
> Para obter informações, consulte [criar uma regra de firewall no nível de banco de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou para determinar o endereço IP usado para a regra de firewall no nível de servidor para seu computador, consulte [criar um firewall de nível de servidor](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Crie seu grupo de recursos e um banco de dados individual usando o portal do Azure.

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite *SQL do Azure* na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
2. Selecione **+ Adicionar** para abrir a página **selecionar opção de implantação do SQL** . Você pode exibir informações adicionais sobre os bancos de dados diferentes selecionando **Mostrar detalhes** no bloco **bancos de dados** .
3. Selecione **criar**:

   ![Criar base de dados individual](../media/sql-database-get-started-portal/create-single-database.png)

3. Na guia **noções básicas** , na seção **detalhes do projeto** , digite ou selecione os seguintes valores:

   - **Assinatura**: menu suspenso e selecione a assinatura correta, se ela não aparecer.
   - **Grupo de recursos**: selecione **criar novo**, digite `myResourceGroup`e selecione **OK**.

     ![Novo banco de dados SQL-guia básico](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Na seção **detalhes do banco de dados** , digite ou selecione os seguintes valores:

   - **Nome do banco de dados**: Insira `mySampleDatabase`.
   - **Servidor**: selecione **criar novo**, insira os valores a seguir e selecione **selecionar**.
       - **Nome do servidor**: tipo `mysqlserver`; junto com alguns números para exclusividade.
       - **Logon de administrador do servidor**: digite `azureuser`.
       - **Senha**: digite uma senha complexa que atenda aos requisitos de senha.
       - **Local**: escolha um local na lista suspensa, como `West US`.

         ![Novo servidor](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Lembre-se de registrar o logon de administrador do servidor e a senha para que você possa fazer logon no servidor e nos bancos de dados para este e outros guias de início rápido. Se você esquecer seu logon ou senha, poderá obter o nome de logon ou redefinir a senha na página do **SQL Server** . Para abrir a página do **SQL Server** , selecione o nome do servidor na página **visão geral** do banco de dados após a criação do banco de dados.

   - **Deseja usar o pool elástico do SQL**: selecione a opção **não** .
   - **Computação + armazenamento**: selecione **configurar banco de dados**. 

     ![Detalhes do banco de dados SQL](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Selecione **provisionado** e **Gen5**.

     ![Gen4 provisionado](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Examine as configurações para **Max vCores**, **min vCores**, **atraso de autopausa**e **tamanho máximo de dados**. Altere-os conforme desejado.
   - Aceite os termos de visualização e clique em **OK**.
   - Selecione **Aplicar**.

5. Selecione a guia **configurações adicionais** . 
6. Na seção **fonte de dados** , em **usar dados existentes**, selecione `Sample`.

   ![Configurações adicionais do banco de BD SQL](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Certifique-se de selecionar os dados de **exemplo (AdventureWorksLT)** para que você possa seguir com facilidade esse e outros guias de início rápido do banco de dados SQL do Azure que usam esse dado.

7. Deixe o restante dos valores como padrão e selecione **revisar + criar** na parte inferior do formulário.
8. Examine as configurações finais e selecione **criar**.

9. No formulário do **banco de dados SQL** , selecione **criar** para implantar e provisionar o grupo de recursos, o servidor e o banco de dados.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Crie seu grupo de recursos e um banco de dados individual usando o PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
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
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
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

Esta parte do artigo usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de banco de dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor lógico. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um novo banco de dados do banco de dados SQL do Azure. | 

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Crie seu grupo de recursos e um banco de dados individual usando AZ CLI.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [conjunto de contas AZ](/cli/azure/account?view=azure-cli-latest#az-account-set) | Define uma assinatura como a assinatura ativa atual. | 
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor de banco de dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [AZ SQL Server Firewall – regra Create](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de um servidor. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Cria um banco de dados. | 


---

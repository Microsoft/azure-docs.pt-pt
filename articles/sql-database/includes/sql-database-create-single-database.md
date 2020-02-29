---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 02/14/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: d800d273cce995c618422a3a9d0934b2657e6ef5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194308"
---
Neste passo, irá criar uma base de dados única Azure SQL. 

> [!IMPORTANT]
> Certifique-se de que configura as regras de firewall para utilizar o endereço IP público do computador que está a usar para completar este artigo.
>
> Para obter informações, consulte [Criar uma regra de firewall de nível de base de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou para determinar o endereço IP utilizado para a regra de firewall do nível do servidor para o seu computador ver Criar uma firewall ao [nível do servidor](../sql-database-server-level-firewall-rule.md).  

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o seu grupo de recursos e uma única base de dados utilizando o portal Azure.

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, *digite o Azure SQL* na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda. 
2. Selecione **+ Adicione** para abrir a página de opção de **implementação Select SQL.** Pode ver informações adicionais sobre as diferentes bases de dados selecionando detalhes do **Show** no azulejo **base de dados.**
3. Selecione **Criar:**

   ![Criar base de dados individual](../media/sql-database-get-started-portal/create-single-database.png)

4. No separador **Basics,** na secção Detalhes do **Projeto,** escreva ou selecione os seguintes valores:

   - **Subscrição**: Desfaça e selecione a subscrição correta, caso não apareça.
   - **Grupo de recursos**: Selecione **Criar novos**, escreva `myResourceGroup`, e selecione **OK**.

     ![Nova base de dados SQL - separador básico](../media/sql-database-get-started-portal/new-sql-database-basics.png)

5. Na secção Dados da **Base de Dados,** escreva ou selecione os seguintes valores:

   - **Nome da base de dados**: Introduza `mySampleDatabase`.
   - **Servidor**: **Selecione Criar novos,** introduza os seguintes valores e, em seguida, selecione **Selecione**.
       - **Nome**do servidor : Tipo `mysqlserver`; juntamente com alguns números para singularidade.
       - Início de **sessão de administração do servidor**: Tipo `azureuser`.
       - **Palavra-passe**: Digite uma senha complexa que satisfaça os requisitos da palavra-passe.
       - **Localização**: Escolha um local a partir da entrega, como `West US`.

         ![Novo servidor](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Lembre-se de gravar o registo do servidor e da palavra-passe para que possa iniciar sessão no servidor e bases de dados para este e outros quickstarts. Se esquecer o seu login ou palavra-passe, pode obter o nome de login ou redefinir a palavra-passe na página do **servidor SQL.** Para abrir a página do **servidor SQL,** selecione o nome do servidor na página de **visão geral** da base de dados após a criação da base de dados.

   - **Deseja utilizar a piscina elástica SQL**: Selecione a opção **No.**
   - **Compute + armazenamento**: Selecione **base de dados Configure**. 

     ![Detalhes da Base de Dados SQL](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Selecione **Provisioned**.  Em alternativa, selecione **Serverless** para criar uma base de dados sem servidores.

     ![Gen4 provisionado](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Reveja as definições para **vCores**, e **tamanho máximo de dados.** Mude isto como desejado. 
     - Opcionalmente, também pode selecionar a **configuração De Alterar** para alterar a geração de hardware.
   - Selecione **Aplicar**.

6. Selecione o separador **Networking** e decida se pretende [**permitir que os serviços e recursos do Azure acedam a este servidor,** ](../sql-database-networkaccess-overview.md)ou adicione um [ponto final privado](../../private-link/private-endpoint-overview.md).

   ![Separador de rede](../media/sql-database-get-started-portal/create-database-networking.png)

7. Selecione o separador de **definições adicionais.** 
8. Na secção **Fonte de Dados,** em **utilizar os dados existentes,** selecione `Sample`.

   ![Configurações adicionais de SQL DB](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Certifique-se de selecionar os dados **da Amostra (AdventureWorksLT)** para que possa seguir facilmente este e outros quickstarts de Base de Dados Azure SQL que utilizam estes dados.

9. Deixe o resto dos valores como padrão e selecione **Rever + Criar** na parte inferior do formulário.
10. Reveja as definições finais e selecione **Criar**.

11. No formulário Base de **Dados SQL,** selecione **Criar** para implementar e fornecer o grupo de recursos, servidor e base de dados.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Crie o seu grupo de recursos e uma única base de dados utilizando o PowerShell.

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

Esta parte do artigo utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de base de dados SQL que acolhe bases de dados únicas e piscinas elásticas. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor lógico. | 
| [Nova AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma nova base de dados única Azure SQL. | 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Crie o seu grupo de recursos e uma única base de dados utilizando o AZ CLI.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $subscription = "<subscriptionID>"
   $randomIdentifier = $(Get-Random)

   $resourceGroup = "resource-$randomIdentifier"
   $location = "East US"
   
   $login = "sampleLogin"
   $password = "samplePassword123!"

   $server = "server-$randomIdentifier"
   $database = "database-$randomIdentifier"
  
   az login # connect to Azure
   az account set -s $subscription # set subscription context for the Azure account

   echo "Creating resource group..."
   az group create --name $resourceGroup --location $location

   echo "Creating primary logical server..."
   az sql server create --name $server --resource-group $resourceGroup --location $location --admin-user $login --admin-password $password

   echo "Creating a gen5 2 vCore database..."
   az sql db create --resource-group $resourceGroup --server $server --name $database --sample-name AdventureWorksLT --edition GeneralPurpose --family Gen5 --capacity 2
   ```

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az conjunto de conta](/cli/azure/account?view=azure-cli-latest#az-account-set) | Define uma subscrição para ser a subscrição ativa atual. | 
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor de base de dados SQL que acolhe bases de dados únicas e piscinas elásticas. |
| [az sql servidor firewall-rule criar](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de um servidor. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Cria uma base de dados. | 


---

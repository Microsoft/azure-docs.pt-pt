---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: eff121cfaf4473607110de4553a9bb8021990caf
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "68444685"
---
Nesta etapa, você criará seu grupo de recursos e um banco de dados SQL do Azure. 

> [!IMPORTANT]
> Certifique-se de configurar as regras de firewall para usar o endereço IP público do computador no qual você está executando as etapas neste artigo. As regras de firewall no nível de banco de dados serão replicadas automaticamente para o servidor secundário.
>
> Para obter informações, consulte [criar uma regra de firewall no nível de banco de dados](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) ou para determinar o endereço IP usado para a regra de firewall no nível de servidor para seu computador, consulte [criar um firewall de nível de servidor](../articles/sql-database/sql-database-server-level-firewall-rule.md).  

# <a name="azure-portaltabazure-portal"></a>[Azure portal](#tab/azure-portal)
Crie seu grupo de recursos e um banco de dados individual usando o portal do Azure. 

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
2. Selecione **bancos** de dados e, em seguida, selecione **SQL Database** para abrir a página **criar banco de dados SQL** .

   ![Criar base de dados individual](../articles/sql-database/media/sql-database-get-started-portal/create-database-1.png)

3. Na guia **noções básicas** , na seção **detalhes do projeto** , digite ou selecione os seguintes valores:

   - **Assinatura**: Clique na lista suspensa e selecione a assinatura correta, se ela não aparecer.
   - **Grupo de recursos**: Selecione **criar novo**, tipo `myResourceGroup`e selecione **OK**.

     ![Novo banco de dados SQL-guia básico](../articles/sql-database/media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Na seção **detalhes do banco de dados** , digite ou selecione os seguintes valores:

   - **Nome do banco de dados**: Introduza `mySampleDatabase`.
   - **Servidor**: Selecione **criar novo** e insira os valores a seguir e selecione **selecionar**.
       - **Nome do servidor**: Digite `mysqlserver`; junto com alguns números para exclusividade.
       - **Logon de administrador do servidor**: Digite `azureuser`.
       - **Senha**: Digite uma senha complexa que atenda aos requisitos de senha.
       - **Local**: Escolha um local na lista suspensa, `West US 2`como.

         ![Novo servidor](../articles/sql-database/media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Lembre-se de registrar o logon de administrador do servidor e a senha para que você possa fazer logon no servidor e nos bancos de dados para este e outros guias de início rápido. Se você esquecer seu logon ou senha, poderá obter o nome de logon ou redefinir a senha na página do **SQL Server** . Para abrir a página do **SQL Server** , selecione o nome do servidor na página **visão geral** do banco de dados após a criação do banco de dados.

        ![Detalhes do banco de dados SQL](../articles/sql-database/media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Deseja usar o pool elástico do SQL**: Selecione a opção **não** .
   - **Computação + armazenamento**: Selecione **configurar banco de dados** e, para este guia de início rápido, selecione **Opções de compra baseadas em vCore**

     ![Opções de compras baseadas em vCore](../articles/sql-database/media/sql-database-get-started-portal/create-database-vcore.png)

   - Selecione **provisionado** e **Gen4**.

     ![camada de computação sem servidor](../articles/sql-database/media/sql-database-get-started-portal/create-database-serverless.png)

   - Examine as configurações para **Max vCores**, **min vCores**, **intervalo de pausa automática**e **tamanho máximo de dados**. Altere-os conforme desejado.
   - Aceite os termos de visualização e clique em **OK**.
   - Selecione **Aplicar**.

5. Selecione a guia **configurações adicionais** . 
6. Na seção **fonte de dados** , em **usar dados existentes**, selecione `Sample`. 

   ![Configurações adicionais do banco de BD SQL](../articles/sql-database/media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Certifique-se de selecionar os dados de **exemplo (AdventureWorksLT)** para que você possa seguir com facilidade esse e outros guias de início rápido do banco de dados SQL do Azure que usam esse dado.

7. Deixe o restante dos valores como padrão e selecione revisar **+ criar** na parte inferior do formulário.
8. Examine as configurações finais e selecione **criar**.

9. No formulário do **banco de dados SQL** , selecione **criar** para implantar e provisionar o grupo de recursos, o servidor e o banco de dados.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](updated-for-az.md)]

Crie seu grupo de recursos e um banco de dados individual usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   $SubscriptionId = '<Your Azure subscription ID>'
   $ResourceGroupName = "myResourceGroup" # to randomize: "myResourceGroup-$(Get-Random)"
   $Location = "westus2"
   $AdminLogin = "azureuser"
   $Password = "ChangeYourAdminPassword1"
   $ServerName = "mysqlserver" # to randomize: "mysqlserver-$(Get-Random)"
   $DatabaseName = "mySampleDatabase"
   
   
   # The ip address range that you want to allow to access your server 
   #(leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"
   
   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   
   # Create a server with a system wide unique server name
   $server = New-AzSqlServer -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -Location $Location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AdminLogin, $(ConvertTo-SecureString -String $Password -AsPlainText -Force))
   
   # Create a server firewall rule that allows access from the specified IP range
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   
   # Create General Purpose Gen4 database with 1 vCore
   $database = New-AzSqlDatabase  -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -DatabaseName $DatabaseName `
      -Edition GeneralPurpose `
      -VCore 1 `
      -ComputeGeneration Gen4  `
      -MinimumCapacity 1 `
      -SampleName "AdventureWorksLT"
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Crie seu grupo de recursos e um banco de dados individual usando AZ CLI. 


   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   export subscriptionID=<Your Subscription ID>
   export ResourceGroupName=myResourceGroup # to randomize: myResourceGroup-$RANDOM
   export Location=WestUS2
   export AdminLogin=azureuser
   export Password=ChangeYourAdminPassword1
   export ServerName="mysqlserver" # to randomize: mysqlserver-$RANDOM
   export DatabaseName=mySampleDatabase
   
   # The ip address range that you want to allow to access your DB. 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   export startip=0.0.0.0
   export endip=0.0.0.0
  
   # Connect to Azure
   az login

   $ Set subscription ID
   az account set --subscription $SubscriptionID
   
   # Create a resource group
   az group create \
      --name $ResourceGroupName \
      --location $Location
   
   # Create a logical server in the resource group
   az sql server create \
      --name $ServerName \
      --resource-group $ResourceGroupName \
      --location $Location  \
      --admin-user $AdminLogin\
      --admin-password $Password
   
   # Configure a firewall rule for the server
   az sql server firewall-rule create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a database in the server 
   az sql db create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      --name $DatabaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen4 \
      --capacity 1 \
   ```

---
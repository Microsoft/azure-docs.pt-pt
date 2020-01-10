---
title: 'Tutorial: adicionar um pool elástico a um grupo de failover'
description: Adicione um pool elástico do banco de dados SQL do Azure a um grupo de failover usando o portal do Azure, o PowerShell ou o CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552564"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Tutorial: adicionar um pool elástico do banco de dados SQL do Azure a um grupo de failover

Configure um grupo de failover para um pool elástico do banco de dados SQL do Azure e failover de teste usando o portal do Azure.  Neste tutorial, vai aprender a:

> [!div class="checklist"]
> - Crie um banco de dados individual do banco de dados SQL do Azure.
> - Adicione o banco de dados individual a um pool elástico. 
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para dois pools elásticos entre dois SQL Servers lógicos.
> - Failover de teste.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem: 

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.


## <a name="1---create-a-single-database"></a>1-criar um banco de dados individual 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-adicionar banco de dados individual ao pool elástico
Nesta etapa, você criará um pool elástico e adicionará seu banco de dados individual a ele. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Crie seu pool elástico usando o portal do Azure. 


1. Selecione **SQL do Azure** no menu à esquerda da portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **selecionar opção de implantação do SQL** . Você pode exibir informações adicionais sobre os bancos de dados diferentes selecionando Mostrar detalhes no bloco bancos de dados.
1. Selecione **pool elástico** na lista suspensa **tipo de recurso** no bloco bancos de **dados SQL** . Selecione **criar** para criar seu pool elástico. 

    ![Selecionar pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Configure seu pool elástico com os seguintes valores:
   - **Nome**: forneça um nome exclusivo para o pool elástico, como `myElasticPool`. 
   - **Assinatura**: selecione sua assinatura na lista suspensa.
   - **Resourcegroup**: selecione `myResourceGroup` na lista suspensa, o grupo de recursos que você criou na seção 1. 
   - **Servidor**: selecione o servidor que você criou na seção 1 na lista suspensa.  

       ![Criar novo servidor para pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Computação + armazenamento**: selecione **Configurar pool elástico** para configurar sua computação, armazenamento e adicionar seu banco de dados individual ao pool elástico. Na guia **configurações de pool** , deixe o padrão de Gen5, com 2 vCores e 32 GB. 

1. Na página **Configurar** , selecione a guia **bancos** de dados e, em seguida, escolha **Adicionar Banco de dados**. Escolha o banco de dados criado na seção 1 e, em seguida, selecione **aplicar** para adicioná-lo ao pool elástico. Selecione **aplicar** novamente para aplicar as configurações de pool elástico e fechar a página **Configurar** . 

    ![Adicionar Banco de BD SQL ao pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selecione **examinar + criar** para examinar as configurações do pool elástico e, em seguida, selecione **criar** para criar seu pool elástico. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Crie seus pools elásticos e o servidor secundário usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool de banco de dados elástico para um banco de dados SQL do Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Define as propriedades de um banco de dados ou move um banco de dados existente para um pool elástico. | 

---

## <a name="3---create-the-failover-group"></a>3-criar o grupo de failover 
Nesta etapa, você criará um [grupo de failover](sql-database-auto-failover-group.md) entre um SQL Server do Azure existente e um novo SQL Server do Azure em outra região. Em seguida, adicione o pool elástico ao grupo de failover. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Crie seu grupo de failover usando o portal do Azure. 

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione o pool elástico criado na seção anterior, como `myElasticPool`. 
1. No painel **visão geral** , selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.
  
    ![Abrir servidor para pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos de failover** no painel **configurações** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Na página **grupo de failover** , insira ou selecione os seguintes valores e, em seguida, selecione **criar**:
    - **Nome do grupo de failover**: digite um nome de grupo de failover exclusivo, como `failovergrouptutorial`. 
    - **Servidor secundário**: selecione a opção para *definir as configurações necessárias* e, em seguida, escolha **criar um novo servidor**. Como alternativa, você pode escolher um servidor já existente como o servidor secundário. Depois de inserir os seguintes valores para o novo servidor secundário, selecione **selecionar**. 
        - **Nome do servidor**: digite um nome exclusivo para o servidor secundário, como `mysqlsecondary`. 
        - **Logon de administrador do servidor**: tipo `azureuser`
        - **Senha**: digite uma senha complexa que atenda aos requisitos de senha.
        - **Local**: escolha um local na lista suspensa, como `East US`. Esse local não pode ser o mesmo local que o servidor primário.

       > [!NOTE]
       > As configurações de logon e firewall do servidor devem corresponder à do seu servidor primário. 
    
       ![Criar um servidor secundário para o grupo de failover](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Selecione **bancos de dados dentro do grupo** e selecione o pool elástico criado na seção 2. Um aviso deve ser exibido, solicitando que você crie um pool elástico no servidor secundário. Selecione o aviso e, em seguida, selecione **OK** para criar o pool elástico no servidor secundário. 
        
    ![Adicionar pool elástico ao grupo de failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selecione **selecionar** para aplicar as configurações de pool elástico ao grupo de failover e, em seguida, selecione **criar** para criar seu grupo de failover. Adicionar o pool elástico ao grupo de failover iniciará automaticamente o processo de replicação geográfica.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Crie seu grupo de failover usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de banco de dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor lógico. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool de banco de dados elástico para um banco de dados SQL do Azure.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo grupo de failover. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais bancos de dados SQL do Azure a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do banco de dados SQL do Azure. |

---


## <a name="4---test-failover"></a>4-failover de teste 
Nesta etapa, você falhará no grupo de failover para o servidor secundário e, em seguida, fará o failback usando o portal do Azure. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Teste o failover de seu grupo de failover usando o portal do Azure. 

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione o pool elástico criado na seção anterior, como `myElasticPool`. 
1. Selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.

    ![Abrir servidor para pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos de failover** no painel **configurações** e escolha o grupo de failover que você criou na seção 2. 
  
   ![Selecione o grupo de failover no portal](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Examine qual servidor é primário e qual servidor é secundário. 
1. Selecione **failover** no painel de tarefas para fazer failover do grupo de failover que contém o pool elástico. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Fazer failover do grupo de failover que contém o banco de dados SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Examine qual servidor é primário, qual servidor é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter funções trocadas. 
1. Selecione **failover** novamente para falhar o grupo de failover de volta para as configurações originais. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Teste o failover de seu grupo de failover usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Falhe o grupo de failover para o servidor secundário e, em seguida, faça o failback usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do banco de dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do banco de dados SQL do Azure. |


---

## <a name="clean-up-resources"></a>Limpar recursos 

Limpe os recursos excluindo o grupo de recursos. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)


1. Navegue até o grupo de recursos na [portal do Azure](https://portal.azure.com).
1. Selecione **excluir grupo de recursos** para excluir todos os recursos no grupo, bem como o próprio grupo de recursos. 
1. Digite o nome do grupo de recursos, `myResourceGroup`, na caixa de texto e, em seguida, selecione **excluir** para excluir o grupo de recursos. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Limpe seus recursos usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Esta parte do tutorial usa o seguinte cmdlet do PowerShell:

| Comando | Notas |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos | 

---

> [!IMPORTANT]
> Se você quiser manter o grupo de recursos, mas excluir o banco de dados secundário, remova-o do grupo de failover antes de excluí-lo. A exclusão de um banco de dados secundário antes que ele seja removido do grupo de failover pode causar um comportamento imprevisível. 

## <a name="full-script"></a>Script completo

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de banco de dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor lógico. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um novo banco de dados do banco de dados SQL do Azure. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool de banco de dados elástico para um banco de dados SQL do Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Define as propriedades de um banco de dados ou move um banco de dados existente para um pool elástico. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo grupo de failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais bancos de dados SQL do Azure a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do banco de dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do banco de dados SQL do Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos | 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Não há nenhum script disponível para o portal do Azure.

---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você adicionou um pool elástico do banco de dados SQL do Azure a um grupo de failover e testou o failover. Aprendeu a:

> [!div class="checklist"]
> - Crie um banco de dados individual do banco de dados SQL do Azure.
> - Adicione o banco de dados individual a um pool elástico. 
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para dois pools elásticos entre dois SQL Servers lógicos.
> - Failover de teste.

Avance para o próximo tutorial sobre como migrar usando o DMS.

> [!div class="nextstepaction"]
> [Tutorial: migrar SQL Server para um banco de dados em pool usando DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)

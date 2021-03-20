---
title: 'Tutorial: Adicione uma piscina elástica a um grupo de failover'
description: Adicione uma piscina elástica Azure SQL Database a um grupo de failover utilizando o portal Azure, PowerShell ou Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: cdbc44158de2f24d7d33d68311979c3b8bdda85d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94593982"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Tutorial: Adicione uma piscina elástica Azure SQL Database a um grupo de failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Configure um grupo de failover para uma piscina elástica Azure SQL Database e teste failover usando o portal Azure.  Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> - Criar uma única base de dados.
> - Adicione a base de dados a uma piscina elástica.
> - Crie um [grupo de failover](auto-failover-group-overview.md) para duas piscinas elásticas entre dois servidores.
> - Teste falhado.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que tem:

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.

## <a name="1---create-a-single-database"></a>1 - Criar uma única base de dados

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 - Adicione a base de dados a uma piscina elástica

Neste passo, irá criar uma piscina elástica e adicionar-lhe a sua base de dados.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie a sua piscina elástica utilizando o portal Azure.

1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva "Azure SQL" na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda.
1. **Selecione + Adicione** para abrir a página de **opção de implementação Select SQL.** Pode ver informações adicionais sobre as diferentes bases de dados selecionando detalhes do Show no azulejo de Bases de Dados.
1. Selecione a **piscina elástica** a partir do **tipo de recurso** drop-down no azulejo **SQL Databases.** Selecione **Criar** para criar a sua piscina elástica.

    ![Selecione piscina elástica](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Configure a sua piscina elástica com os seguintes valores:
   - **Nome**: Forneça um nome único para a sua piscina elástica, tal como `myElasticPool` .
   - **Subscrição**: Selecione a sua subscrição a partir do drop-down.
   - **Grupo de Recursos**: Selecione `myResourceGroup` a partir do drop-down, o grupo de recursos que criou na secção 1.
   - **Servidor**: Selecione o servidor criado na secção 1 a partir do drop-down.  

       ![Crie um novo servidor para piscina elástica](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Cálculo + armazenamento**: Selecione **Configure piscina elástica** para configurar o seu cálculo, armazenamento e adicione a sua única base de dados à sua piscina elástica. No separador **Definições de Pool,** deixe o padrão da Gen5, com 2 vCores e 32gb.

1. Na página **Configurar,** selecione o **separador Bases de Dados** e, em seguida, escolha **adicionar base de dados**. Escolha a base de dados que criou na secção 1 e, em seguida, **selecione Aplicar** para adicioná-la à sua piscina elástica. **Selecione Aplicar** novamente para aplicar as definições de piscina elástica e fechar a página **Configure.**

    ![Adicione base de dados à piscina elástica](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Selecione **Review + crie** para rever as definições de piscina elástica e, em seguida, selecione **Criar** para criar a sua piscina elástica.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie as suas piscinas elásticas e o servidor secundário utilizando o PowerShell.

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

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria uma piscina de base de dados elástica para uma Base de Dados Azure SQL.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Define propriedades para uma base de dados, ou move uma base de dados existente para uma piscina elástica. |

---

## <a name="3---create-the-failover-group"></a>3 - Criar o grupo de failover

Neste passo, irá criar um [grupo de failover](auto-failover-group-overview.md) entre um servidor existente e um novo servidor noutra região. Em seguida, adicione a piscina elástica ao grupo de failover.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o seu grupo de failover utilizando o portal Azure.

1. Selecione **Azure SQL** no menu esquerdo do [portal Azure](https://portal.azure.com). Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda.
1. Selecione a piscina elástica criada na secção anterior, tal como `myElasticPool` .
1. No **painel de visão** geral, selecione o nome do servidor no **nome do Servidor** para abrir as definições do servidor.
  
    ![Servidor aberto para piscina elástica](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos failover** sob o painel **de Definições** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover.

    ![Adicione novo grupo de failover](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. Na página **do Grupo Failover,** introduza ou selecione os seguintes valores e, em seguida, selecione **Criar**:
    - **Nome do grupo failover**: Digite um nome único de grupo de failover, tais como `failovergrouptutorial` .
    - **Servidor secundário**: Selecione a opção para *configurar as definições necessárias* e, em seguida, escolha **criar um novo servidor**. Em alternativa, pode escolher um servidor já existente como servidor secundário. Depois de introduzir os seguintes valores para o seu novo servidor secundário, **selecione**.
        - **Nome do servidor**: Digite um nome único para o servidor secundário, tal como `mysqlsecondary` .
        - **Início de administração do servidor**: Tipo `azureuser`
        - **Palavra-passe**: Digite uma palavra-passe complexa que satisfaça os requisitos de senha.
        - **Localização**: Escolha uma localização a partir da entrega, tal como `East US` . Esta localização não pode ser a mesma localização do seu servidor primário.

       > [!NOTE]
       > As definições de login e firewall do servidor devem corresponder à do seu servidor primário.

       ![Criar um servidor secundário para o grupo de failover](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Selecione **bases de dados dentro do grupo** e, em seguida, selecione a piscina elástica que criou na secção 2. Deve aparecer um aviso, levando-o a criar uma piscina elástica no servidor secundário. Selecione o aviso e, em seguida, selecione **OK** para criar a piscina elástica no servidor secundário. 

   ![Adicione piscina elástica ao grupo failover](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. **Selecione** para aplicar as definições de piscina elástica no grupo failover e, em seguida, selecione **Criar** para criar o seu grupo de failover. A adição da piscina elástica ao grupo failover iniciará automaticamente o processo de geo-replicação.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o seu grupo de failover utilizando o PowerShell.

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
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
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

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que acolhe bases de dados e piscinas elásticas. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria uma piscina elástica para uma Base de Dados Azure SQL.|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo grupo de falhanços. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona uma ou mais bases de dados Azure SQL a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista grupos de failover da Base de Dados Azure SQL. |

---

## <a name="4---test-failover"></a>4 - Teste de failover

Neste passo, falhará o seu grupo de failover para o servidor secundário e, em seguida, falhará de volta usando o portal Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste de falha do seu grupo de failover utilizando o portal Azure.

1. Selecione **Azure SQL** no menu esquerdo do [portal Azure](https://portal.azure.com). Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para o favorito e adicione-a como um item na navegação à esquerda.
1. Selecione a piscina elástica criada na secção anterior, tal como `myElasticPool` .
1. Selecione o nome do servidor no **nome do Servidor** para abrir as definições para o servidor.

    ![Servidor aberto para piscina elástica](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos failover** sob o painel **definições** e, em seguida, escolha o grupo de failover que criou na secção 2.
  
   ![Selecione o grupo de failover a partir do portal](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Reveja qual o servidor primário e qual o servidor secundário.
1. Selecione **Failover** do painel de tarefas para falhar sobre o seu grupo de failover contendo a sua piscina elástica.
1. Selecione **Sim** no aviso que o notifica de que as sessões de TDS serão desligadas.

   ![Falhe sobre o seu grupo de failover contendo a sua base de dados](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Reveja qual o servidor primário, qual o servidor secundário. Se o fracasso foi bem sucedido, os dois servidores deveriam ter trocado de funções.
1. Selecione **Failover** novamente para falhar o grupo de failover de volta às definições originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste de falha do seu grupo de failover usando PowerShell.

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

Falhe o seu grupo de failover para o servidor secundário e, em seguida, falhe de novo usando o PowerShell.

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

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista grupos de failover da Base de Dados Azure SQL. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa uma falha de um grupo de failover da Base de Dados Azure SQL. |

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos eliminando o grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue para o seu grupo de recursos no [portal Azure](https://portal.azure.com).
1. **Selecione Eliminar grupo de recursos** para eliminar todos os recursos do grupo, bem como o próprio grupo de recursos.
1. Digite o nome do grupo de `myResourceGroup` recursos, na caixa de texto e, em seguida, selecione **Eliminar** para eliminar o grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Limpe os seus recursos usando o PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Esta parte do tutorial utiliza o seguinte cmdlet PowerShell:

| Comando | Notas |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos |

---

> [!IMPORTANT]
> Se pretender manter o grupo de recursos, mas eliminar a base de dados secundária, remova-a do grupo de failover antes de a eliminar. A eliminação de uma base de dados secundária antes de ser removida do grupo de failover pode causar comportamentos imprevisíveis.

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que acolhe bases de dados e piscinas elásticas. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma base de dados. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria uma piscina de base de dados elástica para uma Base de Dados Azure SQL.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Define propriedades para uma base de dados, ou move uma base de dados existente para uma piscina elástica. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo grupo de falhanços. |
| [Base de Dados Get-AzSql](/powershell/module/az.sql/get-azsqldatabase) | Obtém uma ou mais bases de dados na Base de Dados SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona uma ou mais bases de dados Azure SQL a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista grupos de failover da Base de Dados Azure SQL. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa uma falha de um grupo de failover da Base de Dados Azure SQL. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Não existem scripts disponíveis para o portal Azure.

---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, adicionou uma piscina elástica Azure SQL Database a um grupo de failover, e testou failover. Aprendeu a:

> [!div class="checklist"]
>
> - Criar uma única base de dados.
> - Adicione a base de dados numa piscina elástica.
> - Crie um [grupo de failover](auto-failover-group-overview.md) para duas piscinas elásticas entre dois servidores.
> - Teste falhado.

Avance para o próximo tutorial sobre como migrar usando DMS.

> [!div class="nextstepaction"]
> [Tutorial: Migrar o SQL Server para uma base de dados em conjunto usando DMS](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)

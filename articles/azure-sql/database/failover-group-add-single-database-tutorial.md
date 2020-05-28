---
title: 'Tutorial: Adicione uma base de dados a um grupo de failover'
description: Adicione uma Base de Dados Azure SQL a um grupo de falhas automáticas utilizando o portal Azure, PowerShell ou Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 78904feac106fbd1b612dfd494a2e8a280b40b25
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051487"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-auto-failover-group"></a>Tutorial: Adicione uma Base de Dados SQL Azure a um grupo de falhas automáticas
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Um [grupo failover](auto-failover-group-overview.md) é uma camada de abstração declarativa que permite agrupar várias bases de dados geo-replicadas. Aprenda a configurar um grupo de failover para uma base de dados Azure SQL e teste failover usando o portal Azure, PowerShell ou Azure CLI.  Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> - Criar uma base de dados na Base de Dados Azure SQL
> - Crie um grupo de falhas para a base de dados entre dois servidores.
> - Falha no teste.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para concluir este tutorial, confirme que tem:

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para completar o tutorial, certifique-se de que tem os seguintes itens:

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para completar o tutorial, certifique-se de que tem os seguintes itens:

- Uma subscrição do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se ainda não tiver uma.
- A versão mais recente do [Azure CLI.](/cli/azure/install-azure-cli?view=azure-cli-latest)

---

## <a name="1---create-a-database"></a>1 - Criar uma base de dados

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Criar o grupo failover

Neste passo, irá criar um grupo de [falhas](auto-failover-group-overview.md) entre um servidor existente e um novo servidor noutra região. Em seguida, adicione a base de dados da amostra ao grupo failover.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie o seu grupo failover e adicione-lhe a sua base de dados utilizando o portal Azure.

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda.
1. Selecione a base de dados criada na secção 1, como `mySampleDatabase` .
1. Os grupos failover podem ser configurados ao nível do servidor. Selecione o nome do servidor sob **o nome do Servidor** para abrir as definições para o servidor.

   ![Servidor aberto para base de dados](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecione **grupos Failover** sob o painel **Definições** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover.

   ![Adicione novo grupo de failover](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. Na página do **Grupo Failover,** introduza ou selecione os seguintes valores e, em seguida, selecione **Criar:**

   - **Nome de grupo failover**: Digite um nome de grupo de failover único, como `failovergrouptutorial` .
   - **Servidor secundário**: Selecione a opção para *configurar as definições necessárias* e, em seguida, escolha **criar um novo servidor**. Em alternativa, pode escolher um servidor já existente como servidor secundário. Depois de introduzir os seguintes valores, selecione **Selecione**.
      - **Nome**do servidor : Digite um nome único para o servidor secundário, como `mysqlsecondary` .
      - **Login de administrador do servidor**: Tipo`azureuser`
      - **Palavra-passe**: Digite uma senha complexa que satisfaça os requisitos da palavra-passe.
      - **Localização**: Escolha um local a partir da entrega, como `East US` . Esta localização não pode ser a mesma localização que o seu servidor principal.

     > [!NOTE]
     > As definições de login do servidor e firewall devem corresponder às do seu servidor principal.

     ![Criar um servidor secundário para o grupo failover](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Bases**de dados dentro do grupo : Uma vez selecionado um servidor secundário, esta opção fica desbloqueada. Selecione-o para Selecionar bases de **dados para adicionar** e, em seguida, escolher a base de dados criada na secção 1. A adição da base de dados ao grupo failover iniciará automaticamente o processo de georeplicação.

   ![Adicione base de dados SQL ao grupo failover](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie o seu grupo de failover e adicione a sua base de dados utilizando o PowerShell.

   > [!NOTE]
   > As definições de login do servidor e firewall devem corresponder às do seu servidor principal.

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [Novo AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor na Base de Dados Azure SQL que acolhe bases de dados únicas e piscinas elásticas. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor na Base de Dados Azure SQL. |
| [Nova AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma nova base de dados única na Base de Dados Azure SQL. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo grupo de failover na Base de Dados Azure SQL. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém uma ou mais bases de dados na Base de Dados Azure SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona uma ou mais bases de dados a um grupo de falhas na Base de Dados Azure SQL. |

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Crie o seu grupo de failover e adicione-lhe a sua base de dados utilizando o Azure CLI.

   > [!NOTE]
   > As definições de login do servidor e firewall devem corresponder às do seu servidor principal.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Esta parte do tutorial utiliza os seguintes cmdlets Azure CLI:

| Comando | Notas |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor que acolhe bases de dados e piscinas elásticas. |
| [az sql servidor firewall-rule criar](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de um servidor. |
| [az sql failover-grupo criar](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Cria um grupo de falhas. |

---

## <a name="3---test-failover"></a>3 - Falha no teste

Neste passo, falhará o seu grupo de failover para o servidor secundário e, em seguida, falhará usando o portal Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste failover usando o portal Azure.

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **Azure SQL** não estiver na lista, selecione **Todos os serviços,** em seguida, digite o Azure SQL na caixa de pesquisa. (Opcional) Selecione a estrela ao lado do **Azure SQL** para a favorita e adicione-a como um item na navegação à esquerda.
1. Selecione a base de dados criada na secção 2, tais como `mySampleDatbase` .
1. Selecione o nome do servidor sob **o nome do Servidor** para abrir as definições para o servidor.

   ![Servidor aberto para base de dados](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecione **os grupos Failover** sob o painel **Definições** e, em seguida, escolha o grupo failover que criou na secção 2.
  
   ![Selecione o grupo failover a partir do portal](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Reveja qual o servidor primário e qual o servidor secundário.
1. Selecione **Failover** do painel de tarefas para falhar no seu grupo de failover contendo a sua base de dados de amostras.
1. Selecione **Sim** no aviso que o avisa de que as sessões de TDS serão desligadas.

   ![Fail over your failover group contendo a sua base de dados SQL](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Reveja qual servidor é agora primário e qual servidor é secundário. Se falharem mais, os dois servidores devem ter trocado de papéis.
1. Selecione **Failover** novamente para falhar os servidores de volta às suas funções originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Teste failover usando PowerShell.

Verifique o papel da réplica secundária:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Falhar no servidor secundário:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName
   ```

Reverter o grupo failover de volta ao servidor primário:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista grupos de falha na Base de Dados Azure SQL. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa uma falha de um grupo de falhas da Base de Dados Azure SQL. |

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Falha no teste utilizando o Azure CLI.

Verifique qual é o servidor secundário:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Falhar no servidor secundário:

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Reverter o grupo failover de volta ao servidor primário:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Esta parte do tutorial utiliza os seguintes cmdlets Azure CLI:

| Comando | Notas |
|---|---|
| [az sql failover-grupo lista](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lista os grupos de failover num servidor. |
| [az sql failover-grupo set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Desloque a primária do grupo failover falhando em todas as bases de dados do servidor primário atual. |

---

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos, apagando o grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Elimine o grupo de recursos utilizando o portal Azure.

1. Navegue para o seu grupo de recursos no [portal Azure.](https://portal.azure.com)
1. **Selecione Eliminar o grupo de recursos** para eliminar todos os recursos do grupo, bem como o próprio grupo de recursos.
1. Digite o nome do grupo de recursos, na caixa de `myResourceGroup` texto, e, em seguida, **selecione Eliminar** para eliminar o grupo de recursos.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Elimine o grupo de recursos utilizando o PowerShell.

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Esta parte do tutorial utiliza os seguintes cmdlets PowerShell:

| Comando | Notas |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos |

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Elimine o grupo de recursos utilizando o Azure CLI.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Esta parte do tutorial utiliza os seguintes cmdlets Azure CLI:

| Comando | Notas |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

---

> [!IMPORTANT]
> Se pretender manter o grupo de recursos mas eliminar a base de dados secundária, remova-a do grupo failover antes de o eliminar. Eliminar uma base de dados secundária antes de ser removido do grupo failover pode causar comportamentos imprevisíveis.

## <a name="full-scripts"></a>Scripts completos

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Novo AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que acolhe bases de dados únicas e piscinas elásticas na Base de Dados Azure SQL. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor na Base de Dados Azure SQL. |
| [Nova AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria uma nova base de dados na Base de Dados Azure SQL. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo grupo de failover na Base de Dados Azure SQL. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém uma ou mais bases de dados na Base de Dados Azure SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona uma ou mais bases de dados a um grupo de falhas na Base de Dados Azure SQL. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista grupos failover na Base de Dados Azure SQL. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa uma falha de um grupo de failover na Base de Dados Azure SQL. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos na Base de Dados Azure SQL.|

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

Este script utiliza os seguintes comandos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az conjunto de conta](/cli/azure/account?view=azure-cli-latest#az-account-set) | Define uma subscrição para ser a subscrição ativa atual. |
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor que acolhe bases de dados únicas e piscinas elásticas na Base de Dados Azure SQL. |
| [az sql servidor firewall-rule criar](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall IP ao nível do servidor na Base de Dados Azure SQL. |
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Cria uma base de dados na Base de Dados Azure SQL. |
| [az sql failover-grupo criar](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Cria um grupo de falhas na Base de Dados Azure SQL. |
| [az sql failover-grupo lista](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lista os grupos failover num servidor na Base de Dados Azure SQL. |
| [az sql failover-grupo set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Desloque a primária do grupo failover falhando em todas as bases de dados do servidor primário atual. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Não existem scripts disponíveis para o portal Azure.

---

Pode encontrar aqui outros scripts de base de dados Azure SQL: [Azure PowerShell](powershell-script-content-guide.md) e [Azure CLI](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Próximos passos

Neste tutorial, adicionou uma Base de Dados Azure SQL a um grupo de failover, e testou falha. Aprendeu a:

> [!div class="checklist"]
>
> - Criar uma base de dados na Base de Dados Azure SQL
> - Crie um grupo de falhas para a base de dados entre dois servidores.
> - Falha no teste.

Avance para o próximo tutorial sobre como adicionar a sua piscina elástica a um grupo de failover.

> [!div class="nextstepaction"]
> [Tutorial: Adicione uma piscina elástica Azure SQL Database a um grupo de failover](failover-group-add-elastic-pool-tutorial.md)

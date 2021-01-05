---
title: Criar uma única base de dados
description: Crie uma única base de dados na Base de Dados Azure SQL utilizando o portal Azure, PowerShell ou o Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/03/2020
ms.openlocfilehash: cb7a441a6d86cd64847300d1e597cf6f86067282
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/23/2020
ms.locfileid: "97740951"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Quickstart: Criar uma base de dados única Azure SQL Database

Neste arranque rápido, cria-se uma [única base de dados](single-database-overview.md) na Base de Dados Azure SQL utilizando o portal Azure, um script PowerShell ou um script Azure CLI. Em seguida, consulta a base de **dados** utilizando o editor de consulta no portal Azure.



## <a name="prerequisite"></a>Pré-requisito

- Uma subscrição ativa do Azure. Se não tiver uma subscrição, [crie uma conta gratuita](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Criar uma única base de dados

Este quickstart cria uma única base de dados no [nível de computação sem servidor](serverless-tier-overview.md).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar uma única base de dados no portal Azure este quickstart começa na página Azure SQL.

1. Navegue na página [de opção De implementação Select SQL.](https://portal.azure.com/#create/Microsoft.AzureSQL)
1. Nas **bases de dados SQL,** deixe o **tipo de recurso** definido para base de **dados única,** e selecione **Criar**.

   ![Adicionar ao Azure SQL](./media/single-database-create-quickstart/select-deployment.png)

1. No **separador Basics** do formulário Criar base de **dados SQL,** nos **detalhes do Projeto,** selecione a **subscrição** Azure desejada.
1. Para **o grupo de recursos**, selecione Criar **novo,** insira *no myResourceGroup* e selecione **OK**.
1. Para **o nome da base de dados** insira *mySampleDatabase*.
1. Para **o Servidor**, selecione Criar **novo** e preencha o novo formulário **do servidor** com os seguintes valores:
   - **Nome do servidor**: *Insira o mysqlserver* e adicione alguns caracteres para singularidade. Não podemos fornecer um nome de servidor exato para usar porque os nomes do servidor devem ser globalmente únicos para todos os servidores em Azure, e não apenas únicos dentro de uma subscrição. Então insira algo como mysqlserver12345, e o portal permite-lhe saber se está disponível ou não.
   - **Início de administração do servidor**: Insira *o azureuser*.
   - **Senha**: Introduza uma palavra-passe que satisfaça os requisitos e introduza-a novamente no campo **de palavra-passe Confirmar.**
   - **Localização**: Selecione uma localização da lista de dropdown.

   Selecione **OK**.

1. Sair **Quero utilizar a piscina elástica SQL** definida para o **nº**.
1. Em **Computação + armazenamento**, selecione a opção para **configurar o servidor**.
1. Este arranque rápido utiliza uma base de dados sem servidor, por isso selecione **Serverless** e, em seguida, **selecione Apply**. 

      ![configure base de dados sem servidor](./media/single-database-create-quickstart/configure-database.png)

1. Selecione **Seguinte: Rede** na parte inferior da página.

   ![Nova base de dados SQL - Separador básico](./media/single-database-create-quickstart/new-sql-database-basics.png)

1. No **separador Rede,** para **o método conectividade,** selecione **Public endpoint**.
1. Para **as regras de Firewall**, desa um endereço IP do cliente **atual** para **Sim**. Deixar **permitir que os serviços e recursos da Azure acedam a este conjunto de servidores** para o **Nº**.
1. Selecione **Seguinte: Definições adicionais** na parte inferior da página.

   ![Separador de rede](./media/single-database-create-quickstart/networking.png)
  

1. No **separador Definições Adicionais,** na secção Fonte de **Dados,** para **utilizar os dados existentes**, selecione **Sample**. Isto cria uma base de dados de amostras AdventureWorksLT para que haja algumas tabelas e dados para consultar e experimentar, em oposição a uma base de dados vazia em branco.
1. Selecione **Review + criar** na parte inferior da página:

   ![Separador de configurações adicionais](./media/single-database-create-quickstart/additional-settings.png)

1. Na página **'Rever + criar',** depois de rever, selecione **Criar**.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com](https://shell.azure.com) . Selecione **Copy** para copiar os blocos de código, cole-o na Cloud Shell e prima **Enter** para executá-lo.

## <a name="set-parameter-values"></a>Definir valores de parâmetros

Os seguintes valores são utilizados em comandos subsequentes para criar a base de dados e os recursos necessários. Os nomes dos servidores precisam de ser globalmente únicos em todo o Azure, pelo que a função $RANDOM é usada para criar o nome do servidor. Substitua os valores 0.0.0.0 no intervalo de endereços IP para corresponder ao ambiente específico.

```azurecli-interactive
# Set the resource group name and location for your server
resourceGroupName=myResourceGroup
location=eastus

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567!

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
serverName=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name $resourceGroupName --location $location
```

## <a name="create-a-server"></a>Criar um servidor

Crie um servidor com o [servidor az sql criar](/cli/azure/sql/server) comando.

```azurecli-interactive
az sql server create \
    --name $serverName \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password
```


## <a name="configure-a-firewall-rule-for-the-server"></a>Configure uma regra de firewall para o servidor

Crie uma regra de firewall com a [regra de firewall do servidor az sql criar](/cli/azure/sql/server/firewall-rule) comando.

```azurecli-interactive
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $serverName \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip
```


## <a name="create-a-single-database-with-azure-cli"></a>Criar uma única base de dados com Azure CLI

Crie uma base de dados com o [comando az sql db.](/cli/azure/sql/db) O seguinte código cria


```azurecli-interactive
az sql db create \
    --resource-group $resourceGroupName \
    --server $serverName \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --compute-model Serverless \
    --family Gen5 \
    --capacity 2
```


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pode criar um grupo de recursos, servidor e base de dados única utilizando o Windows PowerShell.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com](https://shell.azure.com) . Selecione **Copy** para copiar os blocos de código, cole-o na Cloud Shell e prima **Enter** para executá-lo.

## <a name="set-parameter-values"></a>Definir valores de parâmetros

Os seguintes valores são utilizados em comandos subsequentes para criar a base de dados e os recursos necessários. Os nomes dos servidores precisam de ser globalmente únicos em todo o Azure, pelo que o Get-Random cmdlet é usado para criar o nome do servidor. Substitua os valores 0.0.0.0 no intervalo de endereços IP para corresponder ao ambiente específico.

```azurepowershell-interactive
   # Set variables for your server and database
   $resourceGroupName = "myResourceGroup"
   $location = "eastus"
   $adminLogin = "azureuser"
   $password = "Azure1234567!"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName
```


## <a name="create-resource-group"></a>Criar grupo de recursos

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

```azurepowershell-interactive
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
```


## <a name="create-a-server"></a>Criar um servidor

Crie um servidor com o [cmdlet New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver)

```azurepowershell-interactive
  Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server
```

## <a name="create-a-firewall-rule"></a>Criar uma regra de firewall

Crie uma regra de firewall do servidor com o cmdlet [New-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule)

```azurepowershell-interactive
   Write-host "Configuring server firewall rule..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule
```


## <a name="create-a-single-database-with-powershell"></a>Criar uma única base de dados com PowerShell

Crie uma única base de dados com o cmdlet [New-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

```azurepowershell-interactive
   Write-host "Creating a gen5 2 vCore serverless database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeModel Serverless `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
```

---



## <a name="query-the-database"></a>Consultar a base de dados

Uma vez criada a base de dados, pode utilizar o **editor de consulta (pré-visualização)** no portal Azure para ligar à base de dados e consultar dados.

1. No portal, procure e selecione **bases de dados SQL** e, em seguida, selecione a sua base de dados na lista.
1. Na página da sua base de dados, selecione **O editor de consulta (pré-visualização)** no menu esquerdo.
1. Introduza as informações de login do seu servidor e selecione **OK**.

   ![Inscreva-se no editor de consulta](./media/single-database-create-quickstart/query-editor-login.png)

1. Insira a seguinte consulta no painel de **editores de consulta.**

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Selecione **Executar** e analise os resultados da consulta no painel **Resultados**.

   ![Resultados do editor de consulta](./media/single-database-create-quickstart/query-editor-results.png)

1. Feche a página do **editor de consulta** e selecione **OK** quando solicitado para descartar as suas edições não salvas.

## <a name="clean-up-resources"></a>Limpar os recursos

Mantenha o grupo de recursos, servidor e base de dados única para passar aos próximos passos e aprenda a ligar e consultar a sua base de dados com diferentes métodos.

Quando terminar de utilizar estes recursos, pode eliminar o grupo de recursos que criou, que também eliminará o servidor e a base de dados única dentro do mesmo.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para eliminar **o myResourceGroup** e todos os seus recursos utilizando o portal Azure:

1. No portal, procure e selecione **grupos de Recursos** e, em seguida, selecione **myResourceGroup** da lista.
1. Na página do grupo de recursos, selecione **Eliminar o grupo de recursos**.
1. Em **'Digite' o nome do grupo de recursos**, insira o *myResourceGroup* e, em seguida, selecione **Delete**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para eliminar o grupo de recursos e todos os seus recursos, executar o seguinte comando Azure CLI, utilizando o nome do seu grupo de recursos:

```azurecli-interactive
az group delete --name $resourceGroupName
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para eliminar o grupo de recursos e todos os seus recursos, executar o seguinte cmdlet PowerShell, utilizando o nome do seu grupo de recursos:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Passos seguintes

[Conecte e questione a](connect-query-content-reference-guide.md) sua base de dados utilizando diferentes ferramentas e idiomas:
> [!div class="nextstepaction"]
> [Ligar e consultar com o SQL Server Management Studio](connect-query-ssms.md)
>
> [Ligar e consultar com o Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
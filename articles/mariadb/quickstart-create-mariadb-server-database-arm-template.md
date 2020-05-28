---
title: Crie um Azure DB para MariaDB usando um modelo ARM
description: Neste artigo, aprenda a criar uma Base de Dados Azure para o servidor MariaDB utilizando um modelo de Gestor de Recursos Azure.
author: mgblythe
ms.service: mariadb
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 05/14/2020
ms.openlocfilehash: ff7a22c0006d124f8d89b2f933dd213499dc1771
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997576"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Quickstart: Use um modelo ARM para criar uma Base de Dados Azure para servidor MariaDB

A Base de Dados Azure para MariaDB é um serviço gerido que utiliza para executar, gerir e escalar bases de dados MariaDB altamente disponíveis na nuvem. Neste arranque rápido, você usa um modelo ARM para criar uma Base de Dados Azure para servidor MariaDB no portal Azure, PowerShell ou Azure CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)
* Se quiser executar o código localmente, [azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)
* Se quiser executar o código localmente, [azure CLI](/cli/azure/).

---

## <a name="create-an-azure-database-for-mariadb-server"></a>Criar um Azure Database for MariaDB Server

Vai criar um Azure Database for MariaDB Server com um conjunto definido de recursos de armazenamento e computação. Para saber mais, consulte a Base de Dados Azure para os níveis de [preços mariadb.](concepts-pricing-tiers.md) Irá criar o servidor dentro de um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos de quickstart Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mariadb-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json" range="001-231" highlight="149,162,176,199,213":::

O modelo define cinco recursos Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMariaDB/servidores**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft.DBforMariaDB/servidores/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft.DBforMariaDB/servidores/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

Mais bases de dados Azure para amostras de modelo saem em [modelos de arranque rápido de Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implementar a Base de Dados Azure para o modelo de servidor MariaDB no portal Azure:

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

Na base de **dados Deploy Azure para MariaDB com página VNet:**

1. Para **o grupo Recursos**, selecione Criar **novo**, insira um nome para o novo grupo de recursos e selecione **OK**.

2. Se criou um novo grupo de recursos, selecione um **Local** para o grupo de recursos e o novo servidor.

3. Introduza um **Nome de Servidor,** **Registo do Administrador**e **Palavra-passe**de login do administrador .

    ![Implementar base de dados Azure para MariaDB com janela VNet, modelo de quickstart Azure, portal Azure](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. Altere as outras definições predefinidas se quiser:

    * **Subscrição**: a subscrição Azure que pretende utilizar para o servidor.
    * **Capacidade do Sku**: a capacidade vCore, que pode ser *2* (o padrão), *4*, *8*, *16*, *32*ou *64*.
    * **Nome Sku**: prefixo de nível SKU, família SKU e capacidade SKU, acompanhado por sublinhados, tais como *B_Gen5_1,* *GP_Gen5_2* (o padrão) ou *MO_Gen5_32*.
    * **Tamanho Sku MB**: o tamanho do armazenamento, em megabytes, da Base de Dados Azure para o servidor MariaDB (padrão *51200*).
    * **Sku Tier**: o nível de implantação, tais como *Basic*, *GeneralPurpose* (o padrão) ou *MemoryOptimized*.
    * **Família Sku**: *Gen4* ou *Gen5* (o padrão), que indica geração de hardware para implementação do servidor.
    * **Versão Mariadb**: a versão do servidor MariaDB para implementar, como *10.2*, ou *10.3* (o padrão).
    * **Backup Retenção Dias**: o período desejado para retenção de backup geo-redundante, em dias (padrão *7*).
    * **Backup Geo Redundante**: *Habilitado* ou *Desativado* (o padrão), dependendo dos requisitos de recuperação de geo-desastres (Geo-DR).
    * **Nome da rede virtual**: o nome da rede virtual *(azure_mariadb_vnet*padrão ).
    * **Nome da sub-rede**: o nome da sub-rede *(azure_mariadb_subnet*por defeito ).
    * **Nome**da regra da rede virtual : o nome da regra da rede virtual que permite a sub-rede (defeito *AllowSubnet*).
    * **Prefixo de endereço Vnet**: prefixo de endereço para a rede virtual (predefinição *10.0.0.0.0/16*).
    * **Prefixo de sub-rede**: prefixo de endereço para a sub-rede (predefinição *10.0.0.0/16*).

5. Leia os termos e condições e, em seguida, selecione **concordo com os termos e condições acima indicados**.

6. Selecione **Comprar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Utilize o seguinte código interativo para criar uma nova Base de Dados Azure para o servidor MariaDB utilizando o modelo. O código solicita-lhe o novo nome do servidor, o nome e a localização de um novo grupo de recursos e um nome de conta de administrador e senha.

Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MariaDB server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MariaDB server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Utilize o seguinte código interativo para criar uma nova Base de Dados Azure para o servidor MariaDB utilizando o modelo. O código solicita-lhe o novo nome do servidor, o nome e a localização de um novo grupo de recursos e um nome de conta de administrador e senha.

Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for MariaDB server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for MariaDB server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Rever os recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estes passos para ver uma visão geral da sua nova Base de Dados Azure para o servidor MariaDB:

1. No [portal Azure,](https://portal.azure.com)procure e selecione Base de **Dados Azure para servidores MariaDB**.

2. Na lista de bases de dados, selecione o seu novo servidor. Aparece a página **de visão geral** do seu novo servidor Azure Database para o servidor MariaDB.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o seguinte código interativo para ver detalhes sobre a sua Base de Dados Azure para o servidor MariaDB. Terá de introduzir o nome do novo servidor.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o seguinte código interativo para ver detalhes sobre a sua Base de Dados Azure para o servidor MariaDB. Terá de introduzir o nome e o grupo de recursos do novo servidor.

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com)procure e selecione **grupos de Recursos.**

2. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.

3. Na página **'Visão Geral'** do seu grupo de recursos, selecione **Eliminar o grupo de recursos**.

4. Na caixa de diálogo de confirmação, escreva o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Próximos passos

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implante o seu primeiro modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

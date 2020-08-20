---
title: 'Quickstart: Criar um Azure DB para MariaDB - modelo ARM'
description: Neste artigo quickstart, aprenda a criar uma Base de Dados Azure para servidor MariaDB utilizando um modelo de Gestor de Recursos Azure.
author: mgblythe
ms.service: mariadb
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 05/14/2020
ms.openlocfilehash: 108233892a89e6f6288e05476e37460a58b52b0e
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660551"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Quickstart: Use um modelo ARM para criar uma base de dados Azure para servidor MariaDB

A Azure Database for MariaDB é um serviço gerido que utiliza para executar, gerir e escalar bases de dados MariaDB altamente disponíveis na nuvem. Neste arranque rápido, utiliza-se um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma Base de Dados Azure para servidor MariaDB no portal Azure, PowerShell ou Azure CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)
* Se quiser executar o código localmente, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[CLI](#tab/CLI)

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)
* Se quiser executar o código localmente, [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Rever o modelo

Vai criar um Azure Database for MariaDB Server com um conjunto definido de recursos de armazenamento e computação. Para saber mais, consulte [a Base de Dados Azure para os níveis de preços da MariaDB](concepts-pricing-tiers.md). Irá criar o servidor dentro de um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

O modelo define cinco recursos Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMariaDB/servidores**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft.DBforMariaDB/servidores/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft.DBforMariaDB/servidores/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

Mais Azure Database para amostras de modelos MariaDB pode ser encontrado em [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implementar o modelo de servidor Azure Para o servidor MariaDB no portal Azure:

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

Na **Base de Dados de Azure de implantação para MariaDB com página VNet:**

1. Para **o grupo de recursos**, selecione Criar **novo,** insira um nome para o novo grupo de recursos e selecione **OK**.

2. Se criou um novo grupo de recursos, selecione uma **Localização** para o grupo de recursos e para o novo servidor.

3. Introduza um **Nome do Servidor,** **Início de Sessão do Administrador**e **Palavra-passe de Login do Administrador**.

    ![Implementar base de dados Azure para MariaDB com janela VNet, modelo de arranque rápido Azure, portal Azure](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. Altere as outras definições predefinidos se quiser:

    * **Subscrição**: a subscrição Azure que pretende utilizar para o servidor.
    * **Capacidade Sku**: capacidade vCore, que pode ser *2* (o padrão), *4,* *8,* *16,* *32*, ou *64*.
    * **Nome Sku**: o prefixo de nível SKU, a família SKU e a capacidade SKU, unidos por sublinhados, tais como *B_Gen5_1*, *GP_Gen5_2* (o padrão), ou *MO_Gen5_32*.
    * **Tamanho Sku MB**: o tamanho de armazenamento, em megabytes, da Base de Dados Azure para o servidor MariaDB *(padrão 51200).*
    * **Sku Tier**: o nível de implantação, tal como *Basic,* *GeneralPurpose* (o padrão) ou *MemoryOptimed*.
    * **Família Sku**: *Gen4* ou *Gen5* (o padrão), que indica geração de hardware para implementação de servidores.
    * **Versão Mariadb**: a versão do servidor MariaDB a ser implantada, tal como *10.2*, ou *10.3* (o padrão).
    * **Dias de Retenção de Cópia de Segurança**: o período desejado para a retenção de backup geo-redundante, em dias (padrão *7*).
    * **Cópia de segurança geo redundante**: *Ativado* ou *desativado* (o padrão), dependendo dos requisitos de recuperação de geo-desastres (Geo-DR).
    * **Nome da rede virtual**: o nome da rede virtual *(azure_mariadb_vnet*padrão).
    * **Nome da sub-rede**: o nome da sub-rede *(azure_mariadb_subnet*predefinidos ).
    * **Nome da regra da rede virtual**: o nome da regra de rede virtual que permite a sub-rede (padrão Permite a *sub-carteira).*
    * **Prefixo do endereço Vnet**: o prefixo do endereço para a rede virtual *(predefinição 10.0.0.0/16*).
    * **Prefixo do sub-rede**: o prefixo do endereço da sub-rede (predefinição *10.0.0.0/16*).

5. Leia os termos e condições e, em seguida, **selecione concordo com os termos e condições acima indicados**.

6. Selecione **Comprar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Utilize o seguinte código interativo para criar uma nova Base de Dados Azure para o servidor MariaDB utilizando o modelo. O código solicita-lhe o nome do novo servidor, o nome e a localização de um novo grupo de recursos e um nome de conta e senha de conta de administrador.

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

Utilize o seguinte código interativo para criar uma nova Base de Dados Azure para o servidor MariaDB utilizando o modelo. O código solicita-lhe o nome do novo servidor, o nome e a localização de um novo grupo de recursos e um nome de conta e senha de conta de administrador.

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

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estes passos para ver uma visão geral da sua nova Base de Dados Azure para o servidor MariaDB:

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Azure Database para servidores MariaDB**.

2. Na lista de bases de dados, selecione o seu novo servidor. Aparece a página **'Visão Geral'** do seu novo servidor Azure Database para o servidor MariaDB.

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

3. Na página **geral** do seu grupo de recursos, selecione **Delete resource group**.

4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

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

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo ARM, consulte:

> [!div class="nextstepaction"]
> [ Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

---
title: Crie um Azure DB para MySQL usando um modelo ARM
description: Neste artigo, aprenda a criar uma Base de Dados Azure para servidor MySQL com integração de rede virtual, utilizando um modelo de Gestor de Recursos Azure.
services: azure-resource-manager
author: mgblythe
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 04/27/2020
ms.openlocfilehash: 7313d12509859514e41b30c4021f74f25a0e50b9
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629953"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-arm-template"></a>Quickstart: Criar uma Base de Dados Azure para o servidor MySQL utilizando o modelo ARM

A Base de Dados Azure para MySQL é um serviço gerido que utiliza para executar, gerir e escalar bases de dados MySQL altamente disponíveis na nuvem. Este quickstart mostra-lhe como usar um modelo pré-definido do Gestor de Recursos Azure (ARM) para criar uma Base de Dados Azure para servidor MySQL com integração virtual da rede. Pode criar o servidor utilizando o portal Azure, Azure CLI ou Azure PowerShell.

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

## <a name="create-an-azure-database-for-mysql-server"></a>Criar uma Base de Dados do Azure para o servidor MySQL

Irá criar uma Base de Dados do Azure para o servidor MySQL com um conjunto definido de recursos de armazenamento e computação. Para saber mais, consulte a Base de Dados Azure para os níveis de [preços MySQL](concepts-pricing-tiers.md). Irá criar o servidor dentro de um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos de quickstart Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json" range="001-231" highlight="149,162,176,199,213":::

O modelo define cinco recursos Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMySQL/servidores**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft.DBforMySQL/servidores/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft.DBforMySQL/servidores/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

Mais bases de dados Azure para amostras de modelo MySQL podem ser encontradas na galeria de [modelos de arranque rápido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implementar a Base de Dados Azure para o modelo de servidor MySQL no portal Azure:

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

Na base de **dados Azure deploy para MySQL com página VNet:**

1. Para **o grupo Recursos**, selecione Criar **novo**, insira um nome para o novo grupo de recursos e selecione **OK**.

2. Se criou um novo grupo de recursos, selecione um **Local** para o grupo de recursos e o novo servidor.

3. Introduza um **Nome de Servidor,** **Registo do Administrador**e **Palavra-passe**de login do administrador .

    ![Implementar base de dados Azure para MySQL com janela VNet, modelo de quickstart Azure, portal Azure](./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png)

4. Altere as outras definições predefinidas se quiser:

    * **Subscrição**: a subscrição Azure que pretende utilizar para o servidor.
    * **Capacidade do Sku**: a capacidade vCore, que pode ser *2* (o padrão), *4*, *8*, *16*, *32*ou *64*.
    * **Nome Sku**: prefixo de nível SKU, família SKU e capacidade SKU, acompanhado por sublinhados, tais como *B_Gen5_1,* *GP_Gen5_2* (o padrão) ou *MO_Gen5_32*.
    * **Tamanho Sku MB**: o tamanho do armazenamento, em megabytes, da Base de Dados Azure para o servidor MySQL (padrão *5120*).
    * **Sku Tier**: o nível de implantação, tais como *Basic*, *GeneralPurpose* (o padrão) ou *MemoryOptimized*.
    * **Família Sku**: *Gen4* ou *Gen5* (o padrão), que indica geração de hardware para implementação do servidor.
    * **Versão Mysql**: a versão do servidor MySQL para implementar, tais como *5.6* ou *5.7* (o padrão).
    * **Backup Retenção Dias**: o período desejado para retenção de backup geo-redundante, em dias (padrão *7*).
    * **Backup Geo Redundante**: *Habilitado* ou *Desativado* (o padrão), dependendo dos requisitos de recuperação de geo-desastres (Geo-DR).
    * **Nome da rede virtual**: o nome da rede virtual *(azure_mysql_vnet*padrão ).
    * **Nome da sub-rede**: o nome da sub-rede *(azure_mysql_subnet*por defeito ).
    * **Nome**da regra da rede virtual : o nome da regra da rede virtual que permite a sub-rede (defeito *AllowSubnet*).
    * **Prefixo de endereço Vnet**: prefixo de endereço para a rede virtual (predefinição *10.0.0.0.0/16*).
    * **Prefixo de sub-rede**: prefixo de endereço para a sub-rede (predefinição *10.0.0.0/16*).

5. Leia os termos e condições e, em seguida, selecione **concordo com os termos e condições acima indicados**.

6. Selecione **Comprar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Utilize o seguinte código interativo para criar uma nova Base de Dados Azure para o servidor MySQL utilizando o modelo. O código solicita-lhe o novo nome do servidor, o nome e a localização de um novo grupo de recursos e um nome de conta de administrador e senha.

Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Utilize o seguinte código interativo para criar uma nova Base de Dados Azure para o servidor MySQL utilizando o modelo. O código solicita-lhe o novo nome do servidor, o nome e a localização de um novo grupo de recursos e um nome de conta de administrador e senha.

Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Rever os recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estes passos para ver uma visão geral da sua nova Base de Dados Azure para o servidor MySQL:

1. No [portal Azure,](https://portal.azure.com)procure e selecione Base de **Dados Azure para servidores MySQL**.

2. Na lista de bases de dados, selecione o seu novo servidor. Aparece a página **de visão geral** do seu novo servidor De base de dados Azure para o mySQL.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o seguinte código interativo para visualizar detalhes sobre a sua Base de Dados Azure para o servidor MySQL. Terá de introduzir o nome do novo servidor.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o seguinte código interativo para visualizar detalhes sobre a sua Base de Dados Azure para o servidor MySQL. Terá de introduzir o nome e o grupo de recursos do novo servidor.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
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
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Crie e implante o seu primeiro modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

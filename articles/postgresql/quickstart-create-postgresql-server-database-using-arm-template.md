---
title: 'Quickstart: Criar um Azure DB para modelo PostgreSQL - ARM'
description: Neste quickstart, aprenda a criar uma Base de Dados Azure para servidor único PostgreSQL utilizando um modelo de Gestor de Recursos Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/11/2021
ms.openlocfilehash: fb9f12b3b31f1049cd4d9306294783e514331229
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382196"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Quickstart: Use um modelo ARM para criar uma base de dados Azure para PostgreSQL - servidor único

A Base de Dados do Azure para o PostgreSQL é um serviço gerido com o qual pode executar, gerir e dimensionar as bases de dados de elevada disponibilidade do PostgreSQL na cloud. Neste arranque rápido, utiliza-se um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma Base de Dados Azure para PostgreSQL - servidor único no portal Azure, PowerShell ou Azure CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Desdobre-se para Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

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

Cria uma Base de Dados Azure para servidor PostgreSQL com um conjunto configurado de recursos de computação e armazenamento. Para saber mais, consulte [os níveis de preços na Base de Dados Azure para PostgreSQL - Single Server](concepts-pricing-tiers.md). Irá criar o servidor dentro de um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md).

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-managed-postgresql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-postgresql-with-vnet/azuredeploy.json":::

O modelo define cinco recursos Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforPostgreSQL/servidores**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft.DBforPostgreSQL/servidores/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft.DBforPostgreSQL/servidores/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

Mais Azure Database para amostras de modelos postgreSQL pode ser encontrado em [modelos de arranque rápido Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implementar o modelo de servidor Azure Database para o servidor PostgreSQL no portal Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implementar no Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

Na **Base de Dados de Azure de implementação para postgreSQL com página VNet:**

1. Para **o grupo de recursos**, selecione Criar **novo,** insira um nome para o novo grupo de recursos e selecione **OK**.

2. Se criou um novo grupo de recursos, selecione uma **Localização** para o grupo de recursos e para o novo servidor.

3. Introduza um **Nome do Servidor,** **Início de Sessão do Administrador** e **Palavra-passe de Login do Administrador**.

    :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png" alt-text="Implementar base de dados de Azure para PostgreSQL com janela VNet, modelo de arranque rápido Azure, portal Azure":::

4. Altere as outras definições predefinidos se quiser:

    * **Subscrição**: a subscrição Azure que pretende utilizar para o servidor.
    * **Capacidade Sku**: capacidade vCore, que pode ser *2* (o padrão), *4,* *8,* *16,* *32*, ou *64*.
    * **Nome Sku**: o prefixo de nível SKU, a família SKU e a capacidade SKU, unidos por sublinhados, tais como *B_Gen5_1*, *GP_Gen5_2* (o padrão), ou *MO_Gen5_32*.
    * **Tamanho Sku MB**: o tamanho de armazenamento, em megabytes, da Base de Dados Azure para servidor PostgreSQL *(padrão 51200).*
    * **Sku Tier**: o nível de implantação, tal como *Basic,* *GeneralPurpose* (o padrão) ou *MemoryOptimed*.
    * **Família Sku**: *Gen4* ou *Gen5* (o padrão), que indica geração de hardware para implementação de servidores.
    * **Versão pós-quadrado**: a versão do servidor PostgreSQL a ser implantada, tal como *9.5*, *9.6*, *10* ou *11* (o padrão).
    * **Dias de Retenção de Cópia de Segurança**: o período desejado para a retenção de backup geo-redundante, em dias (padrão *7*).
    * **Cópia de segurança geo redundante**: *Ativado* ou *desativado* (o padrão), dependendo dos requisitos de recuperação de geo-desastres (Geo-DR).
    * **Nome da rede virtual**: o nome da rede virtual *(azure_postgresql_vnet* padrão).
    * **Nome da sub-rede**: o nome da sub-rede *(azure_postgresql_subnet* predefinidos ).
    * **Nome da regra da rede virtual**: o nome da regra de rede virtual que permite a sub-rede (padrão Permite a *sub-carteira).*
    * **Prefixo do endereço Vnet**: o prefixo do endereço para a rede virtual *(predefinição 10.0.0.0/16*).
    * **Prefixo do sub-rede**: o prefixo do endereço da sub-rede (predefinição *10.0.0.0/16*).

5. Leia os termos e condições e, em seguida, **selecione concordo com os termos e condições acima indicados**.

6. Selecione **Comprar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Utilize o seguinte código interativo para criar uma nova Base de Dados Azure para servidor PostgreSQL utilizando o modelo. O código solicita-lhe o nome do novo servidor, o nome e a localização de um novo grupo de recursos e um nome de conta e senha de conta de administrador.

Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Utilize o seguinte código interativo para criar uma nova Base de Dados Azure para servidor PostgreSQL utilizando o modelo. O código solicita-lhe o nome do novo servidor, o nome e a localização de um novo grupo de recursos e um nome de conta e senha de conta de administrador.

Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estes passos para ver uma visão geral da sua nova Base de Dados Azure para o servidor PostgreSQL:

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Azure Database para servidores PostgreSQL**.

2. Na lista de bases de dados, selecione o seu novo servidor. Aparece a página **'Visão Geral'** do seu novo servidor PostgreSQL.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o seguinte código interativo para ver detalhes sobre a sua Base de Dados Azure para servidor PostgreSQL. Terá de introduzir o nome do novo servidor.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o seguinte código interativo para ver detalhes sobre a sua Base de Dados Azure para servidor PostgreSQL. Terá de introduzir o nome e o grupo de recursos do novo servidor.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="exporting-arm-template-from-the-portal"></a>Modelo ARM de exportação a partir do portal
Pode [exportar um modelo ARM](../azure-resource-manager/templates/export-template-portal.md) a partir do portal Azure. Existem duas formas de exportar um modelo:

- [Exportação de grupo de recursos ou recursos.](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) Esta opção gera um novo modelo a partir dos recursos existentes. O modelo exportado é um "instantâneo" do estado atual do grupo de recursos. Pode exportar um grupo de recursos inteiro ou recursos específicos dentro desse grupo de recursos.
- [Exportar antes da implantação ou da história.](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment) Esta opção recupera uma cópia exata de um modelo utilizado para a implantação.

Ao exportar o modelo, na ```"properties":{ }```  secção do recurso do servidor PostgreSQL irá notar isso e não será incluído por ```administratorLogin``` ```administratorLoginPassword``` razões de segurança. **Tem de** adicionar estes parâmetros ao seu modelo antes de implementar o modelo ou o modelo falhará.

```
"resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
```



## <a name="clean-up-resources"></a>Limpar os recursos

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

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [ Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

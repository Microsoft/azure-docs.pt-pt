---
title: 'Quickstart: Criar um serviço Azure SignalR - modelo ARM'
description: Neste Quickstart, aprenda a criar um Serviço Azure SignalR utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: zhshang
ms.date: 10/02/2020
ms.openlocfilehash: 4ab029048b37a4dcb44ef405249dcb9e20de70cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841634"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Quickstart: Use um modelo ARM para implementar o Serviço Azure SignalR

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (modelo ARM) para criar um serviço Azure SignalR. Pode implantar o Serviço Azure SignalR através do portal Azure, PowerShell ou CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo abrir-se-á no portal Azure assim que iniciar seduca.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Botão para implantar o Serviço Azure SignalR para Azure utilizando um modelo ARM no portal Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)
* Se quiser executar o código localmente, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/)
* Se quiser executar o código localmente:
    * Uma concha bash (como Git Bash, que está incluída em [Git para Windows).](https://gitforwindows.org)
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

O modelo define um recurso Azure:

* [**Microsoft.SignalrService/Signalr**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implementar o Serviço Azure SignalR utilizando o modelo ARM no portal Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Botão para implantar o Serviço Azure SignalR para Azure utilizando o modelo ARM no portal Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Na página **de Serviço Azure SignalR:**

1. Se desejar, altere a **Subscrição** do padrão.

2. Para **o grupo de recursos**, selecione Criar **novo,** insira um nome para o novo grupo de recursos e selecione **OK**.

3. Se criou um novo grupo de recursos, selecione uma **Região** para o grupo de recursos.

4. Se desejar, insira um novo **Nome** e a **Localização** (como **eastus2)** do Serviço Azure SignalR. Se não especificar um nome, é gerado automaticamente. A localização do Serviço Azure SignalR pode ser igual ou diferente da região do grupo de recursos. Se não especificar uma localização, está definida para a mesma região que o grupo de recursos.

5. Escolha o **Nível de Preços** **(Free_F1** ou **Standard_S1),** introduza a **Capacidade** (número de unidades SignalR) e escolha um Modo de **Serviço** de **Predefinição** (requer servidor de hub), **Serverless** (não permite qualquer ligação ao servidor) ou **Clássico** (encaminhado para o servidor do hub apenas se o hub tiver ligação ao servidor). Em seguida, escolha se ativar **registos de conectividade** ou **ativar registos de mensagens**.

    > [!NOTE]
    > Para o nível de preços **Free_F1,** a capacidade é limitada a 1 unidade.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Screenshot do modelo ARM para criar um Serviço Azure SignalR no portal Azure.":::

6. Selecione **Rever + criar**.

7. Leia os termos e condições e, em seguida, **selecione Criar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Se quiser executar os scripts PowerShell localmente, insira primeiro `Connect-AzAccount` para configurar as suas credenciais Azure.

Utilize o seguinte código para implantar o Serviço Azure SignalR utilizando o modelo ARM. O código solicita-lhe os seguintes itens:

* O nome e região do novo Serviço Azure SignalR
* O nome e a região de um novo grupo de recursos
* O nível de preços Azure **(Free_F1** ou **Standard_S1)**
* Capacidade da unidade SignalR (1, 2, 5, 10, 20, 50 ou 100)
  > [!NOTE]
  > Para o nível de preços **Free_F1,** a capacidade é limitada a 1 unidade.
* O modo de serviço: **Predefinido** exigir um servidor de hub, **Serverless** para não permitir qualquer ligação ao servidor, ou **Clássico** para encaminhar para um servidor hub apenas se o hub tiver uma ligação de servidor
* Se permitir registos de conectividade ou mensagens **(verdadeiros** ou **falsos)**

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Utilize o seguinte código para implantar o Serviço Azure SignalR utilizando o modelo ARM. O código solicita-lhe os seguintes itens:

* O nome e região do novo Serviço Azure SignalR
* O nome e a região de um novo grupo de recursos
* O nível de preços Azure **(Free_F1** ou **Standard_S1)**
* Capacidade da unidade SignalR (1, 2, 5, 10, 20, 50 ou 100)
    > [!NOTE]
    > Para o nível de preços **Free_F1,** a capacidade é limitada a 1 unidade.
* O modo de serviço: **Predefinido** exigir um servidor de hub, **Serverless** para não permitir qualquer ligação ao servidor, ou **Clássico** para encaminhar para um servidor hub apenas se o hub tiver uma ligação de servidor
* Se permitir registos de conectividade ou mensagens **(verdadeiros** ou **falsos)**

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> A implementação pode demorar alguns minutos. Note os nomes do Serviço Azure SignalR e do grupo de recursos, que utiliza para rever os recursos implantados mais tarde.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estes passos para ver uma visão geral do seu novo Serviço Azure SignalR:

1. No [portal Azure,](https://portal.azure.com)procure e selecione **SignalR**.

2. Na lista SignalR, selecione o seu novo serviço. Aparece **a página de visão geral** do novo Serviço Azure SignalR.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o seguinte código interativo para ver detalhes sobre o seu Serviço Azure SignalR. Terá de introduzir o nome do novo serviço e do grupo de recursos.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o seguinte código interativo para ver detalhes sobre o seu Serviço Azure SignalR. Terá de introduzir o nome do novo serviço e do grupo de recursos.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com)procure e selecione **grupos de Recursos.**

2. Na lista de grupos de recursos, escolha o nome do seu grupo de recursos.

3. Na página **geral** do seu grupo de recursos, selecione **Delete resource group**.

4. Na caixa de diálogo de confirmação, digite o nome do seu grupo de recursos e, em seguida, selecione **Delete**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo ARM, consulte:

> [!div class="nextstepaction"]
> [ Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

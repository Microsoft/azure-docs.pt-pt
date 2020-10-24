---
title: 'Quickstart: Implementar API Azure para FHIR usando um modelo ARM'
description: Neste quickstart, aprenda a implementar a API Azure para Recursos de Interoperabilidade de Cuidados rápidos de Saúde (FHIR®), utilizando um modelo de Gestor de Recursos Azure (modelo ARM).
author: mgblythe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 10/06/2020
ms.openlocfilehash: 0d045aac63daf99bf67ec79438f55eedd5f200d9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521446"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Quickstart: Use um modelo ARM para implantar API Azure para FHIR

Neste arranque rápido, você aprenderá a usar um modelo de Gestor de Recursos Azure (modelo ARM) para implementar API Azure para Recursos de Interoperabilidade de Cuidados rápidos de Saúde (FHIR®). Pode implementar a Azure API para FHIR através do portal Azure, PowerShell ou CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo abrir-se-á no portal Azure assim que iniciar seduca.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implemente para Azure uma API Azure para o serviço FHIR utilizando um modelo ARM no portal Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

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

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/).

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

O modelo define um recurso Azure:

* **Microsoft.HealthcareApis/serviços**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>Implementar o modelo

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecione o seguinte link para implementar a API Azure para FHIR utilizando o modelo ARM no portal Azure:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Implemente para Azure uma API Azure para o serviço FHIR utilizando um modelo ARM no portal Azure.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

Na **API de Azure para** a página FHIR:

1. Se desejar, altere a **Subscrição** do padrão para uma subscrição diferente.

2. Para **o grupo de recursos**, selecione Criar **novo,** insira um nome para o novo grupo de recursos e selecione **OK**.

3. Se criou um novo grupo de recursos, selecione uma **Região** para o grupo de recursos.

4. Introduza um novo **Nome de Serviço** e escolha a **localização** da API Azure para FHIR. A localização pode ser a mesma que ou diferente da região do grupo de recursos.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Implemente para Azure uma API Azure para o serviço FHIR utilizando um modelo ARM no portal Azure.":::

5. Selecione **Rever + criar**.

6. Leia os termos e condições e, em seguida, **selecione Criar**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Se quiser executar os scripts PowerShell localmente, insira primeiro `Connect-AzAccount` para configurar as suas credenciais Azure.

Se o `Microsoft.HealthcareApis` fornecedor de recursos ainda não estiver registado para a sua subscrição, pode registá-lo com o seguinte código interativo. Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Utilize o seguinte código para implantar a API Azure para o serviço FHIR utilizando o modelo ARM. O código solicita-lhe o novo nome de serviço FHIR, o nome de um novo grupo de recursos e as localizações para cada um deles.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Se o `Microsoft.HealthcareApis` fornecedor de recursos ainda não estiver registado para a sua subscrição, pode registá-lo com o seguinte código interativo. Para executar o código em Azure Cloud Shell, selecione **Experimente-o** no canto superior de qualquer bloco de código.

```azurecli-interactive
az extension add --name healthcareapis
```

Utilize o seguinte código para implantar a API Azure para o serviço FHIR utilizando o modelo ARM. O código solicita-lhe o novo nome de serviço FHIR, o nome de um novo grupo de recursos e as localizações para cada um deles.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> A implantação leva alguns minutos para ser concluída. Note os nomes da Azure API para o serviço FHIR e o grupo de recursos, que utiliza para rever os recursos implantados mais tarde.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

# <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estes passos para ver uma visão geral do seu novo AZure API para o serviço FHIR:

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Azure API para FHIR**.

2. Na lista FHIR, selecione o seu novo serviço. Aparece a **página geral** do novo serviço Azure API para o serviço FHIR.

3. Para validar que a nova conta FHIR API é a forada, selecione o link ao lado do **ponto final de metadados FHIR** para obter a declaração de capacidade da FHIR API. O link tem um formato de `https://<service-name>.azurehealthcareapis.com/metadata` . Se a conta for provisionada, é apresentado um grande ficheiro JSON.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Execute o seguinte código interativo para ver detalhes sobre o seu Azure API para o serviço FHIR. Terá de introduzir o nome do novo serviço e do grupo de recursos.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Execute o seguinte código interativo para ver detalhes sobre o seu Azure API para o serviço FHIR. Terá de introduzir o nome do novo serviço e do grupo de recursos.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
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

Para um tutorial passo a passo que o guia através do processo de criação de um modelo ARM, consulte o [tutorial para criar e implementar o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Passos seguintes

Neste guia de arranque rápido, colocou a API Azure para fHIR na sua subscrição. Para definir definições adicionais na sua API Azure para FHIR, proceda às definições adicionais como orientar. Se estiver pronto para começar a usar a API Azure para FHIR, leia mais sobre como registar as aplicações.

>[!div class="nextstepaction"]
>[Definições adicionais em API Azure para FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Visão geral das candidaturas do registo](fhir-app-registration.md)

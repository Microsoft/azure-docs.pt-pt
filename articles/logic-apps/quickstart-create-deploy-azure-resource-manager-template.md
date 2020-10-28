---
title: Quickstart - Crie e implemente o fluxo de trabalho de aplicações lógicas utilizando modelos de Gestor de Recursos Azure
description: Como criar e implementar uma aplicação lógica usando modelos de Gestor de Recursos Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.date: 06/30/2020
ms.openlocfilehash: 689582d73e29cb60cc2ee5294b568b5db2f73244
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739336"
---
# <a name="quickstart-create-and-deploy-a-logic-app-workflow-by-using-an-arm-template"></a>Quickstart: Criar e implementar um fluxo de trabalho de aplicações lógicas usando um modelo ARM

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) é um serviço de cloud que o ajuda a criar e executar fluxos de trabalho automatizados que integram dados, apps, serviços baseados na nuvem e sistemas no local, selecionando a partir [de centenas de conectores.](/connectors/connector-reference/connector-reference-logicapps-connectors) Este quickstart foca-se no processo de implementação de um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma aplicação lógica básica que verifica o estado do Azure em um horário de hora. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure** . O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

Este quickstart utiliza o modelo [**de aplicação lógica Create,**](https://azure.microsoft.com/resources/templates/101-logic-app-create/) que pode encontrar na [Galeria de Modelos Azure Quickstart,](https://azure.microsoft.com/resources/templates) mas é muito tempo para ser mostrado aqui. Em vez disso, pode rever o ["azuredeploy.json file"](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) do modelo de arranque rápido na galeria de modelos.

O modelo de arranque rápido cria um fluxo de trabalho de aplicações lógicas que utiliza o gatilho Recorrence, que está programado para ser executado a cada hora, e uma [ação *incorporada*](../connectors/apis-list.md#connector-types)HTTP , que chama um URL que devolve o estado de Azure. Uma ação incorporada é nativa da plataforma Azure Logic Apps.

Este modelo cria o seguinte recurso Azure:

* [**Microsoft.Logic/workflows,**](/azure/templates/microsoft.logic/workflows)que cria o fluxo de trabalho para uma aplicação lógica.

Para encontrar modelos de arranque mais rápidos para apps Azure Logic, reveja os modelos [Microsoft.Logic](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Logic) na galeria.

<a name="deploy-template"></a>

## <a name="deploy-the-template"></a>Implementar o modelo

Siga a opção que pretende utilizar para implementar o modelo de arranque rápido:

| Opção | Descrição |
|--------|-------------|
| [Portal do Azure](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-portal#deploy-template) | Se o seu ambiente Azure satisfaça os requisitos pré-requisitos e estiver familiarizado com a utilização de modelos ARM, estes passos ajudam-no a iniciar diretamente o Azure e a abrir o modelo de arranque rápido no portal Azure. Para obter mais informações, consulte [implementar recursos com modelos ARM e portal Azure.](../azure-resource-manager/templates/deploy-portal.md) |
| [CLI do Azure](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-cli#deploy-template) | A interface de linha de comando Azure (Azure CLI) é um conjunto de comandos para a criação e gestão de recursos Azure. Para executar estes comandos, precisa da versão 2.6 ou posterior do Azure CLI. Para verificar a sua versão CLI, escreva `az --version` . Para obter mais informações, veja estes tópicos: <p><p>- [O que é Azure CLI](/cli/azure/what-is-azure-cli?view=azure-cli-latest) <br>- [Começa com o Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) |
| [Azure PowerShell](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=azure-powershell#deploy-template) | O Azure PowerShell fornece um conjunto de cmdlets que utilizam o modelo do Azure Resource Manager para gerir os recursos do Azure. Para obter mais informações, veja estes tópicos: <p><p>- [Visão geral da Azure PowerShell](/powershell/azure/azurerm/overview) <br>- [Introduzindo o módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az) <br>- [Começa com o Azure PowerShell](/powershell/azure/get-started-azureps) |
| [Azure Resource Management REST API](../logic-apps/quickstart-create-deploy-azure-resource-manager-template.md?tabs=rest-api#deploy-template) | O Azure fornece APIs de Transferência do Estado Representativo (REST), que são pontos finais de serviço que suportam operações HTTP (métodos) que utiliza para criar, recuperar, atualizar ou apagar o acesso aos recursos de serviço. Para mais informações, consulte [Começar com a AZure REST API.](/rest/api/azure/) |
|||

<a name="deploy-azure-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione a seguinte imagem para iniciar sômbro com a sua conta Azure e abra o modelo de arranque rápido no portal Azure:

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-logic-app-create%2fazuredeploy.json)

1. No portal, na **aplicação lógica Criar uma página de modelo,** insira ou selecione estes valores:

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Subscrição** | <*Nome de subscrição Azure*> | O nome para a subscrição Azure para usar |
   | **Grupo de recursos** | <*Nome de grupo Azure-recursos*> | O nome de um novo grupo de recursos Azure ou existente. Este exemplo `Check-Azure-Status-RG` utiliza. |
   | **Região** | <*Região de Azure*> | A região do datacenter Azure para usar a sua aplicação lógica. Este exemplo `West US` utiliza. |
   | **Nome de aplicativo lógica** | <*lógica-app-nome*> | O nome a usar para a sua aplicação lógica. Este exemplo `Check-Azure-Status-LA` utiliza. |
   | **Teste Uri** | <*test-URI*> | O URI para o serviço para ligar com base num horário específico. Este exemplo `https://status.azure.com/en-us/status/` utiliza, que é a página de estado do Azure. |
   | **Localização** |  <*Azure-região-para-todos os recursos*> | A região de Azure para usar para todos os recursos, se diferente do valor padrão. Este exemplo utiliza o valor predefinido, `[resourceGroup().location]` que é a localização do grupo de recursos. |
   ||||

   Aqui está como a página fica com os valores usados neste exemplo:

   ![Fornecer informações para o modelo de arranque rápido](./media/quickstart-create-deploy-azure-resource-manager-template/create-logic-app-template-portal.png)

1. Quando terminar, selecione **Review + create** .

1. Continue com os passos em [Revisão dos recursos mobilizados.](#review-deployed-resources)

#### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name name to use for generating resource names:" projectName &&
read -p "Enter the location, such as 'westus':" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

Para obter mais informações, veja estes tópicos:

* [Azure CLI: grupo de implantação az](/cli/azure/deployment/group)
* [Implementar recursos com modelos ARM e Azure CLI](../azure-resource-manager/templates/deploy-cli.md)

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name to use for generating resource names"
$location = Read-Host -Prompt "Enter the location, such as 'westus'"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

Para obter mais informações, veja estes tópicos:

* [Azure PowerShell: New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)
* [Azure PowerShell: New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [Implementar recursos com modelos ARM e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

#### <a name="rest-api"></a>[API REST](#tab/rest-api)

1. Se não quiser utilizar um grupo de recursos Azure existente, crie um novo grupo de recursos seguindo esta sintaxe para o pedido que envia para a API de Gestão de Recursos:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
   ```

   | Valor | Descrição |
   |-------|-------------|
   | `subscriptionId`| O GUID para a subscrição Azure que pretende utilizar |
   | `resourceGroupName` | O nome para o grupo de recursos Azure criar. Este exemplo `Check-Azure-Status-RG` utiliza. |
   |||

   Por exemplo:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG?api-version=2019-10-01
   ```

   Para obter mais informações, veja estes tópicos:

   * [Referência API AZURE REST - Como chamar APIs de DESCANSO Azure](/rest/api/azure/)
   * [Gestão de recursos REST API: Grupos de recursos - Criar ou atualizar](/rest/api/resources/resourcegroups/createorupdate).

1. Para implementar o modelo de arranque rápido para o seu grupo de recursos, siga esta sintaxe para o pedido que enviar para a API de Gestão de Recursos:

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

   | Valor | Descrição |
   |-------|-------------|
   | `subscriptionId`| O GUID para a subscrição Azure que pretende utilizar |
   | `resourceGroupName` | O nome para o grupo de recursos Azure a usar. Este exemplo `Check-Azure-Status-RG` utiliza. |
   | `deploymentName` | O nome a usar para a sua implantação. Este exemplo `Check-Azure-Status-LA` utiliza. |
   |||

   Por exemplo:

   ```http
   PUT https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourcegroups/Check-Azure-Status-RG/providers/Microsoft.Resources/deployments/Check-Azure-Status-LA?api-version=2019-10-01
   ```

   Para obter mais informações, consulte [a API REST de Gestão de Recursos: Implementações - Criar ou Atualizar](/rest/api/resources/deployments/createorupdate).

1. Para fornecer os valores a utilizar para a implantação, como a região Azure e ligações ao modelo de arranque rápido e [arquivo de parâmetros](../azure-resource-manager/templates/template-parameters.md), que contém os valores para o modelo de arranque rápido a utilizar na implementação, siga esta sintaxe para o órgão de pedido que envia para a API de Gestão de Recursos:

   ```json
   {
      "location": "{Azure-region}",
      "properties": {
         "templateLink": {
            "uri": "{quickstart-template-URL}",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "{quickstart-template-parameter-file-URL}",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | `location`| <*Região de Azure*> | A região de Azure para usar para implantação. Este exemplo `West US` utiliza. |
   | `templateLink` : `uri` | <*quickstart-modelo-URL*> | A localização do URL para o modelo de arranque rápido a utilizar para implantação: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json`. |
   | `parametersLink` : `uri` | <*quickstart-modelo-parâmetro-FILE-URL*> | A localização do URL para o ficheiro de parâmetro do modelo de arranque rápido para usar para implantação: <p><p>`https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json` <p><p>Para obter mais informações sobre o ficheiro de parâmetro do Gestor de Recursos, consulte estes tópicos: <p><p>- [Criar ficheiro de parâmetro do Gestor de Recursos](../azure-resource-manager/templates/parameter-files.md) <br>- [Tutorial: Use ficheiros de parâmetros para implementar o seu modelo ARM](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md) |
   | `mode` | <*modo de implantação*> | Executar uma atualização incremental ou uma atualização completa. Este exemplo `Incremental` utiliza, que é o valor padrão. Para obter mais informações, consulte [os modos de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/deployment-modes.md). |
   |||

   Por exemplo:

   ```json
   {
      "location": "West US",
      "properties": {
         "templateLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json",
            "contentVersion": "1.0.0.0"
         },
         "parametersLink": {
            "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.parameters.json",
            "contentVersion": "1.0.0.0"
         },
         "mode": "Incremental"
      }
   }
   ```

Para obter mais informações, veja estes tópicos:

* [Gestão de Recursos REST API](/rest/api/resources/)
* [Implementar recursos com modelos ARM e Gestor de Recursos REST API](../azure-resource-manager/templates/deploy-rest.md)

---

<a name="review-deployed-resources"></a>

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Para visualizar a aplicação lógica, pode utilizar o portal Azure, executar um script que cria com Azure CLI ou Azure PowerShell, ou utilizar a API da App Lógica REST.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na caixa de pesquisa do portal Azure, insira o nome da sua aplicação lógica, que está `Check-Azure-Status-LA` neste exemplo. Na lista de resultados, selecione a sua aplicação lógica.

1. No portal Azure, encontre e selecione a sua app lógica, que está `Check-Azure-Status-RG` neste exemplo.

1. Quando o Logic App Designer abrir, reveja a aplicação lógica criada pelo modelo quickstart.

1. Para testar a aplicação lógica, na barra de ferramentas do designer, selecione **Run** .

### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter your logic app name:" &&
read logicAppName &&
az logic workflow show --name $logicAppName &&
echo "Press [ENTER] to continue ..."
```

Para mais informações, consulte [Azure CLI: az logic workflow show](/cli/azure/ext/logic/logic/workflow?view=azure-cli-latest#ext-logic-az-logic-workflow-show).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$logicAppName = Read-Host -Prompt "Enter your logic app name"
Get-AzLogicApp -Name $logicAppName
Write-Host "Press [ENTER] to continue..."
```

Para mais informações, consulte [Azure PowerShell: Get-AzLogicApp](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="rest-api"></a>[API REST](#tab/rest-api)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}?api-version=2016-06-01
```

| Valor | Descrição |
|-------|-------------|
| `subscriptionId`| O GUID para a subscrição Azure onde implementou o modelo de arranque rápido. |
| `resourceGroupName` | O nome do grupo de recursos Azure onde implementou o modelo de arranque rápido. Este exemplo `Check-Azure-Status-RG` utiliza. |
| `workflowName` | O nome da aplicação lógica que implementou. Este exemplo `Check-Azure-Status-LA` utiliza. |
|||

Por exemplo:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG/providers/Microsoft.Logic/workflows/Check-Azure-Status-LA?api-version=2016-06-01
```

Para obter mais informações, consulte [As Aplicações Lógicas REST API: Workflows - Get](/rest/api/logic/workflows/get).

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se planeia continuar a trabalhar com os acessos rápidos e tutoriais subsequentes, talvez queira manter estes recursos. Quando já não precisar da aplicação lógica, elimine o grupo de recursos utilizando o portal Azure CLI, Azure PowerShell ou a API de Gestão de Recursos.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No portal Azure, encontre e selecione o grupo de recursos que pretende eliminar, que está `Check-Azure-Status-RG` neste exemplo.

1. No menu do grupo de recursos, selecione **Overview** se ainda não estiver selecionado. Na página geral, selecione **Eliminar o grupo de recursos** .

1. Para confirmar, insira o nome para o grupo de recursos.

Para obter mais informações, consulte [Eliminar o grupo de recursos](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

### <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter your resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

Para obter mais informações, consulte [Azure CLI: az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Para obter mais informações, consulte [Azure PowerShell: Remove-AzResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

### <a name="rest-api"></a>[API REST](#tab/rest-api)

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2019-10-01
```

| Valor | Descrição |
|-------|-------------|
| `subscriptionId`| O GUID para a subscrição Azure onde implementou o modelo de arranque rápido. |
| `resourceGroupName` | O nome do grupo de recursos Azure onde implementou o modelo de arranque rápido. Este exemplo `Check-Azure-Status-RG` utiliza. |
|||

Por exemplo:

```http
GET https://management.azure.com/subscriptions/xxxxXXXXxxxxXXXXX/resourceGroups/Check-Azure-Status-RG?api-version=2019-10-01
```

Para obter mais informações, consulte [a API REST Gestão de Recursos: Grupos de Recursos - Eliminar](/rest/api/resources/resourcegroups/delete).

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

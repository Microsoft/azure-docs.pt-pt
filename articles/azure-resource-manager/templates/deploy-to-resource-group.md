---
title: Mobilizar recursos para grupos de recursos
description: Descreve como implementar recursos num modelo de Gestor de Recursos Azure. Mostra como direcionar mais do que um grupo de recursos.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: c3401346f31d34d92da1f52ca79f691e94e7eb78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99491559"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Implementações de grupos de recursos com modelos ARM

Este artigo descreve como estender a sua implementação a um grupo de recursos. Utilize um modelo de Gestor de Recursos Azure (modelo ARM) para a implementação. O artigo também mostra como expandir o âmbito para além do grupo de recursos na operação de implantação.

## <a name="supported-resources"></a>Recursos suportados

A maioria dos recursos pode ser implantada num grupo de recursos. Para obter uma lista de recursos disponíveis, consulte [a referência do modelo ARM](/azure/templates).

## <a name="schema"></a>Esquema

Para modelos, utilize o seguinte esquema:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Para ficheiros de parâmetros, utilize:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Comandos de implantação

Para implantar num grupo de recursos, utilize os comandos de implantação do grupo de recursos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para o Azure CLI, utilize [o grupo de implantação Az .](/cli/azure/deployment/group#az_deployment_group_create) O exemplo a seguir implementa um modelo para criar um grupo de recursos:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para o comando de implantação PowerShell, utilize [o New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). O exemplo a seguir implementa um modelo para criar um grupo de recursos:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Para obter informações mais detalhadas sobre comandos de implantação e opções para a implementação de modelos ARM, consulte:

* [Implementar recursos com modelos ARM e portal Azure](deploy-portal.md)
* [Implementar recursos com modelos ARM e Azure CLI](deploy-cli.md)
* [Implementar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md)
* [Implementar recursos com modelos ARM e AZure Resource Manager REST API](deploy-rest.md)
* [Use um botão de implementação para implementar modelos do repositório GitHub](deploy-to-azure-button.md)
* [Implementar modelos ARM da Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Âmbitos de implantação

Ao ser implantado num grupo de recursos, pode mobilizar recursos para:

* o grupo de recursos-alvo da operação
* outros grupos de recursos na mesma subscrição ou outras subscrições
* qualquer subscrição no inquilino
* o inquilino para o grupo de recursos

Um [recurso de extensão](scope-extension-resources.md) pode ser traçado para um alvo diferente do alvo de implantação.

O utilizador que implementa o modelo deve ter acesso ao âmbito especificado.

Esta secção mostra como especificar diferentes âmbitos. Você pode combinar estes diferentes âmbitos em um único modelo.

### <a name="scope-to-target-resource-group"></a>Âmbito para o grupo de recursos-alvo

Para implantar recursos no recurso-alvo, adicione esses recursos à secção de recursos do modelo.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

Para obter um modelo de exemplo, consulte [Implementar para o grupo de recursos alvo](#deploy-to-target-resource-group).

### <a name="scope-to-resource-group-in-same-subscription"></a>Âmbito para grupo de recursos na mesma subscrição

Para implementar recursos para um grupo de recursos diferente na mesma subscrição, adicione uma implantação aninhada e inclua a `resourceGroup` propriedade. Se não especificar o ID de subscrição ou o grupo de recursos, o grupo de subscrição e recursos do modelo principal são utilizados. Todos os grupos de recursos devem existir antes de executar a implantação.

No exemplo seguinte, a implantação aninhada visa um grupo de recursos chamado `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

Para obter um modelo de exemplo, consulte [Implementar em vários grupos de recursos](#deploy-to-multiple-resource-groups).

### <a name="scope-to-resource-group-in-different-subscription"></a>Âmbito para grupo de recursos em subscrição diferente

Para implementar recursos para um grupo de recursos numa subscrição diferente, adicione uma implementação aninhada e inclua as `subscriptionId` `resourceGroup` propriedades e propriedades. No exemplo seguinte, a implantação aninhada visa um grupo de recursos chamado `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

Para obter um modelo de exemplo, consulte [Implementar em vários grupos de recursos](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Âmbito de subscrição

Para implementar recursos para uma subscrição, adicione uma implantação aninhada e inclua a `subscriptionId` propriedade. A subscrição pode ser a subscrição para o grupo de recursos-alvo, ou qualquer outra subscrição no inquilino. Além disso, desa cos um conjunto de `location` imóveis para a implantação aninhada.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

Para obter um modelo de exemplo, consulte [Criar grupo de recursos.](#create-resource-group)

### <a name="scope-to-tenant"></a>Âmbito para inquilino

Para criar recursos no inquilino, estabeleça `scope` `/` o. O utilizador que implementa o modelo deve ter o [acesso necessário para implantar no arrendatário](deploy-to-tenant.md#required-access).

Para utilizar uma implantação aninhada, definido `scope` e `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

Ou, pode definir o âmbito `/` para alguns tipos de recursos, como grupos de gestão.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

Para mais informações, consulte [o Grupo de Gestão.](deploy-to-management-group.md#management-group)

## <a name="deploy-to-target-resource-group"></a>Implementar para o grupo de recursos-alvo

Para implantar recursos no grupo de recursos-alvo, defina esses recursos na `resources` secção do modelo. O modelo a seguir cria uma conta de armazenamento no grupo de recursos que é especificado na operação de implantação.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>Implementar em vários grupos de recursos

Pode implantar em mais de um grupo de recursos num único modelo ARM. Para direcionar um grupo de recursos diferente do modelo dos pais, use um [modelo aninhado ou ligado.](linked-templates.md) Dentro do tipo de recurso de implementação, especifique os valores para o ID de subscrição e o grupo de recursos para o qual pretende que o modelo aninhado seja implantado. Os grupos de recursos podem existir em diferentes subscrições.

> [!NOTE]
> Pode implantar **800 grupos de recursos** numa única implantação. Normalmente, esta limitação significa que pode implementar para um grupo de recursos especificado para o modelo principal, e até 799 grupos de recursos em implementações aninhadas ou ligadas. No entanto, se o seu modelo principal contiver apenas modelos aninhados ou ligados e não implementar quaisquer recursos, então pode incluir até 800 grupos de recursos em implementações aninhadas ou ligadas.

O exemplo a seguir implementa duas contas de armazenamento. A primeira conta de armazenamento é implantada para o grupo de recursos especificado na operação de implantação. A segunda conta de armazenamento é implantada no grupo de recursos especificado nos `secondResourceGroup` parâmetros e `secondSubscriptionID` parâmetros:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Se definir `resourceGroup` o nome de um grupo de recursos que não existe, a implementação falha.

Para testar o modelo anterior e ver os resultados, utilize o PowerShell ou o Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma subscrição,** utilize:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Para implantar duas contas de armazenamento em **duas subscrições,** utilize:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para implantar duas contas de armazenamento em dois grupos de recursos na **mesma subscrição,** utilize:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Para implantar duas contas de armazenamento em **duas subscrições,** utilize:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>Criar grupo de recursos

A partir de uma implementação de grupo de recursos, pode mudar para o nível de uma subscrição e criar um grupo de recursos. O modelo a seguir implanta uma conta de armazenamento no grupo de recursos-alvo e cria um novo grupo de recursos na subscrição especificada.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

* Para um exemplo de implantação de configurações de espaço de trabalho para o Centro de Segurança Azure, consulte [deployASCwithWorkspaceSettings.jsem](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).

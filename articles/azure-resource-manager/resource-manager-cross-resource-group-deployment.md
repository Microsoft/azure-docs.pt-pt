---
title: Implantar o grupo de recursos & assinatura cruzada de recursos do Azure
description: Mostra como direcionar mais de uma assinatura do Azure e um grupo de recursos durante a implantação.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: c90096043f54eb8db5834fbe83ed1d6ae710d371
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528322"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Implantar recursos do Azure em mais de uma assinatura ou grupo de recursos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Normalmente, você implanta todos os recursos em seu modelo em um único [grupo de recursos](resource-group-overview.md). No entanto, há cenários em que você deseja implantar um conjunto de recursos juntos, mas colocá-los em diferentes grupos de recursos ou assinaturas. Por exemplo, talvez você queira implantar a máquina virtual de backup para Azure Site Recovery em um grupo de recursos e um local separados. O Gerenciador de recursos permite que você use modelos aninhados para direcionar diferentes assinaturas e grupos de recursos do que a assinatura e o grupo de recursos usados para o modelo pai.

> [!NOTE]
> Você pode implantar em apenas cinco grupos de recursos em uma única implantação. Normalmente, essa limitação significa que você pode implantar em um grupo de recursos especificado para o modelo pai e até quatro grupos de recursos em implantações aninhadas ou vinculadas. No entanto, se o modelo pai contiver apenas modelos aninhados ou vinculados e não implantar nenhum recurso, você poderá incluir até cinco grupos de recursos em implantações aninhadas ou vinculadas.

## <a name="specify-a-subscription-and-resource-group"></a>Especificar uma assinatura e um grupo de recursos

Para direcionar um recurso diferente, use um modelo aninhado ou vinculado. O tipo de recurso `Microsoft.Resources/deployments` fornece parâmetros para `subscriptionId` e `resourceGroup`. Essas propriedades permitem que você especifique uma assinatura e um grupo de recursos diferentes para a implantação aninhada. Todos os grupos de recursos devem existir antes da execução da implantação. Se você não especificar a ID da assinatura ou o grupo de recursos, a assinatura e o grupo de recursos do modelo pai serão usados.

A conta usada para implantar o modelo deve ter permissões para implantar a ID de assinatura especificada. Se a assinatura especificada existir em um locatário Azure Active Directory diferente, você deverá [Adicionar usuários convidados de outro diretório](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Para especificar um grupo de recursos e uma assinatura diferentes, use:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Se os grupos de recursos estiverem na mesma assinatura, você poderá remover o valor **SubscriptionId** .

O exemplo a seguir implanta duas contas de armazenamento: uma no grupo de recursos especificado durante a implantação e outra em um grupo de recursos especificado no parâmetro `secondResourceGroup`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Se você definir `resourceGroup` como o nome de um grupo de recursos que não existe, a implantação falhará.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Usar as funções resourcegroup () e Subscription ()

Para implantações entre grupos de recursos, as funções [resourcegroup ()](resource-group-template-functions-resource.md#resourcegroup) e [Subscription ()](resource-group-template-functions-resource.md#subscription) são resolvidas de forma diferente com base em como você especifica o modelo aninhado. 

Se você inserir um modelo dentro de outro modelo, as funções no modelo aninhado são resolvidas para o grupo de recursos pai e a assinatura. Um modelo inserido usa o seguinte formato:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

Se você vincular a um modelo separado, as funções no modelo vinculado resolverão o grupo de recursos e a assinatura aninhados. Um modelo vinculado usa o seguinte formato:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>Modelos de exemplo

Os modelos a seguir demonstram várias implantações de grupo de recursos. Os scripts para implantar os modelos são mostrados após a tabela.

|Modelo  |Descrição  |
|---------|---------|
|[Modelo de assinatura cruzada](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Implanta uma conta de armazenamento em um grupo de recursos e uma conta de armazenamento em um segundo grupo de recursos. Inclua um valor para a ID da assinatura quando o segundo grupo de recursos estiver em uma assinatura diferente. |
|[Modelo de propriedades de grupo de recursos cruzado](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Demonstra como a função `resourceGroup()` resolve. Ele não implanta nenhum recurso. |

### <a name="powershell"></a>PowerShell

Para o PowerShell, para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura**, use:

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

Para o PowerShell, para implantar duas contas de armazenamento em **duas assinaturas**, use:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Para o PowerShell, para testar como o **objeto do grupo de recursos** é resolvido para o modelo pai, o modelo embutido e o modelo vinculado, use:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

No exemplo anterior, **parentRG** e **inlineRG** resolvem para **Parent**. **linkedRG** resolve para o **linked**. A saída do exemplo anterior é:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>CLI do Azure

Para CLI do Azure, para implantar duas contas de armazenamento em dois grupos de recursos na **mesma assinatura**, use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Para CLI do Azure, para implantar duas contas de armazenamento em **duas assinaturas**, use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Por CLI do Azure, para testar como o **objeto do grupo de recursos** é resolvido para o modelo pai, o modelo embutido e o modelo vinculado, use:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

No exemplo anterior, **parentRG** e **inlineRG** resolvem para **Parent**. **linkedRG** resolve para o **linked**. A saída do exemplo anterior é:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Passos seguintes

* Para entender como definir parâmetros em seu modelo, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obter dicas sobre como resolver erros comuns de implantação, consulte [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para obter informações sobre como implantar um modelo que requer um token SAS, consulte [implantar o modelo privado com o token SAS](resource-manager-powershell-sas-token.md).

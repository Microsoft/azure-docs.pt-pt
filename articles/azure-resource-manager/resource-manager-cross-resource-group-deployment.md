---
title: Implemente recursos do Azure a vários grupos de recursos e subscrição | Documentos da Microsoft
description: Mostra como mais de um grupo de recursos e subscrição do Azure de destino durante a implementação.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: 3641833f0b55f20066302de350bfab17adfade0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128531"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Implemente recursos do Azure para mais de uma subscrição ou grupo de recursos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Normalmente, implementa todos os recursos no seu modelo para uma única [grupo de recursos](resource-group-overview.md). No entanto, existem cenários onde pretende implementar um conjunto de recursos em conjunto, mas colocá-los em diferentes grupos de recursos ou subscrições. Por exemplo, pode querer implementar a máquina de virtual de cópia de segurança do Azure Site Recovery para um grupo de recursos separado e a localização. O Resource Manager permite-lhe utilizar modelos aninhados para diferentes subscrições de destino e grupos de recursos que a subscrição e grupo de recursos utilizado para o modelo de elemento principal.

> [!NOTE]
> Pode implementar em apenas cinco grupos de recursos numa única implementação. Normalmente, esta limitação significa que pode implementar um grupo de recursos especificado para o modelo de principal e até quatro grupos de recursos em implementações aninhadas ou ligados. No entanto, se seu modelo principal contém apenas os modelos de ligado ou aninhados e faz não próprio implementar todos os recursos, em seguida, pode incluir até cinco grupos de recursos em implementações aninhadas ou ligadas.

## <a name="specify-a-subscription-and-resource-group"></a>Especifique um grupo de recursos e subscrição

Para filtrar um recurso diferente, utilize um modelo aninhado ou ligado. O `Microsoft.Resources/deployments` tipo de recurso fornece parâmetros para `subscriptionId` e `resourceGroup`. Estas propriedades permitem-lhe especificar um grupo de recursos e subscrição diferente para a implementação aninhada. Todos os grupos de recursos tem de existir antes de executar a implementação. Se não especificar o grupo de recursos ou ID de subscrição, a subscrição e grupo de recursos do modelo principal é utilizado.

A conta que utiliza para implementar o modelo tem de ter permissões para implementar para o ID da subscrição especificada. Se a subscrição especificada existe noutro inquilino do Azure Active Directory, tem [adicionar utilizadores convidados a partir de outro diretório](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Para especificar um grupo de recursos diferente e uma subscrição, utilize:

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

Se os seus grupos de recursos estão na mesma subscrição, pode remover o **subscriptionId** valor.

O exemplo seguinte implementa duas contas de armazenamento - um no grupo de recursos especificado durante a implementação, e um num grupo de recursos especificado no `secondResourceGroup` parâmetro:

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

Se definir `resourceGroup` para o nome do grupo de recursos que não existe, a implementação falhar.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Utilize as funções resourceGroup() e subscription()

Para várias implementações do grupo de recursos, o [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) e [subscription()](resource-group-template-functions-resource.md#subscription) funções resolver diferentemente com base nas como especificar o modelo aninhado. 

Se incorporar um modelo dentro de outro modelo, as funções no modelo aninhado resolver para o grupo de recursos de principal e a subscrição. Um modelo incorporado utiliza o seguinte formato:

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

Se associar a um modelo separado, as funções no modelo ligado resolver-se para a subscrição e grupo de recursos aninhados. Um modelo ligado utiliza o seguinte formato:

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

Os seguintes modelos de demonstram várias implementações de grupo de recursos. Scripts para implementar os modelos são apresentados depois da tabela.

|Modelo  |Descrição  |
|---------|---------|
|[Entre o modelo de subscrição](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Implementa uma conta de armazenamento para um grupo de recursos e uma conta de armazenamento para um segundo grupo de recursos. Inclua um valor para o ID de subscrição quando o segundo grupo de recursos está numa subscrição diferente. |
|[Entre o modelo de propriedades do grupo de recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Demonstra como o `resourceGroup()` resolve o objetivo de função. Não implementa todos os recursos. |

### <a name="powershell"></a>PowerShell

Para o PowerShell, para implementar duas contas de armazenamento em dois grupos de recursos no **mesma subscrição**, utilize:

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

Para o PowerShell, para implementar duas contas de armazenamento para **duas subscrições**, utilize:

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

Para o PowerShell, para testar como o **objeto de grupo de recursos** é resolvido para o modelo principal, o modelo inline e o modelo ligado, de utilização:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

No exemplo anterior, ambos **parentRG** e **inlineRG** resolver para **parentGroup**. **linkedRG** é resolvido para o **linkedGroup**. O resultado do exemplo anterior é:

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

Para a CLI do Azure, para implementar duas contas de armazenamento em dois grupos de recursos no **mesma subscrição**, utilize:

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

Para a CLI do Azure, para implementar duas contas de armazenamento para **duas subscrições**, utilize:

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

Para a CLI do Azure, para testar como o **objeto de grupo de recursos** é resolvido para o modelo principal, o modelo inline e o modelo ligado, de utilização:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

No exemplo anterior, ambos **parentRG** e **inlineRG** resolver para **parentGroup**. **linkedRG** é resolvido para o **linkedGroup**. O resultado do exemplo anterior é:

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

## <a name="next-steps"></a>Passos Seguintes

* Para compreender como definir parâmetros no seu modelo, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).
* Para obter dicas sobre como resolver erros comuns de implementação, consulte [resolver erros comuns de implementação do Azure com o Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para obter informações sobre como implementar um modelo que necessita de um token SAS, consulte [implementar modelo privado com o SAS token](resource-manager-powershell-sas-token.md).

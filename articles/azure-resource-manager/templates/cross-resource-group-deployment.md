---
title: Implementar recursos cruze subscrição & grupo de recursos
description: Mostra como direcionar mais de um grupo de subscrição e recursos Azure durante a implantação.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 70868f5a3598c26ffff81f0ad3536a6c5c0a7e53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460352"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Dispor recursos do Azure para mais de um grupo de subscrição ou recursos

Normalmente, você implementa todos os recursos do seu modelo para um único [grupo de recursos.](../management/overview.md) No entanto, existem cenários em que pretende utilizar um conjunto de recursos, mas colocá-los em diferentes grupos de recursos ou subscrições. Por exemplo, pode querer implementar a máquina virtual de backup para a Recuperação do Site Azure para um grupo e localização de recursos separados. O Gestor de Recursos permite-lhe utilizar modelos aninhados para direcionar mais do que um grupo de subscrição e recursos.

> [!NOTE]
> Pode implantar apenas cinco grupos de recursos numa única implantação. Normalmente, esta limitação significa que pode implantar-se num grupo de recursos especificado para o modelo de progenitor, e até quatro grupos de recursos em implementações aninhadas ou ligadas. No entanto, se o seu modelo de progenitor contiver apenas modelos aninhados ou ligados e não implementar quaisquer recursos, então pode incluir até cinco grupos de recursos em implementações aninhadas ou ligadas.

## <a name="specify-subscription-and-resource-group"></a>Especificar grupo de subscrição e recursos

Para direcionar um grupo de recursos ou subscrição diferente, utilize um [modelo aninhado ou ligado](linked-templates.md). O `Microsoft.Resources/deployments` tipo de recurso `subscriptionId` fornece `resourceGroup`parâmetros para e, que lhe permitem especificar o grupo de subscrição e recursos para a implantação aninhada. Se não especificar o ID de subscrição ou o grupo de recursos, o grupo de subscrição e recursos do modelo de progenitor é utilizado. Todos os grupos de recursos devem existir antes de executar a implantação.

A conta que utiliza para implementar o modelo deve ter permissões para implantar no ID de subscrição especificado. Se a subscrição especificada existir num inquilino do Diretório Ativo Azure diferente, deve [adicionar utilizadores convidados de outro diretório](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Para especificar um grupo de recursos diferente e subscrição, utilize:

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

Se os seus grupos de recursos estiverem na mesma subscrição, pode remover o valor **de subscriçãoId.**

O exemplo seguinte implementa duas contas de armazenamento. A primeira conta de armazenamento é implantada para o grupo de recursos especificado durante a implantação. A segunda conta de armazenamento é implantada `secondResourceGroup` `secondSubscriptionID` para o grupo de recursos especificado nos e parâmetros:

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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2017-06-01",
      "name": "[variables('firstStorageName')]",
      "location": "[resourceGroup().location]",
      "sku":{
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate",
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
            "apiVersion": "2017-06-01",
            "name": "[variables('secondStorageName')]",
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
    }
  ]
}
```

Se definir `resourceGroup` o nome de um grupo de recursos que não existe, a implementação falha.

Para testar o modelo anterior e ver os resultados, utilize powerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para implementar duas contas de armazenamento para dois grupos de recursos na **mesma subscrição,** use:

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

Para implementar duas contas de armazenamento em **duas subscrições,** use:

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para implementar duas contas de armazenamento para dois grupos de recursos na **mesma subscrição,** use:

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

Para implementar duas contas de armazenamento em **duas subscrições,** use:

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

## <a name="use-functions"></a>Utilizar funções

As funções do Grupo de [Recursos()](template-functions-resource.md#resourcegroup) e [da subscrição](template-functions-resource.md#subscription) resolvem de forma diferente com base na forma como especifica o modelo. Quando se liga a um modelo externo, as funções resolvem-se sempre ao âmbito desse modelo. Quando nidificar um modelo dentro `expressionEvaluationOptions` de um modelo de progenitor, use a propriedade para especificar se as funções resolvem o grupo de recursos e a subscrição para o modelo dos pais ou o modelo aninhado. Desloque `inner` a propriedade para resolver o âmbito para o modelo aninhado. Desloque `outer` a propriedade para resolver o âmbito do modelo de progenitor.

O quadro seguinte mostra se as funções resolvem para o grupo de recursos e subscrição incorporados.

| Tipo de modelo | Âmbito | Resolução |
| ------------- | ----- | ---------- |
| aninhada        | exterior (padrão) | Grupo de recursos-mãe |
| aninhada        | interior | Grupo de subrecursos |
| ligado        | N/D   | Grupo de subrecursos |

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) de exemplo mostra:

* modelo aninhado com âmbito padrão (exterior)
* modelo aninhado com âmbito interior
* modelo ligado

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "defaultScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "innerScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "expressionEvaluationOptions": {
          "scope": "inner"
      },
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "resourceGroup": "linkedGroup",
      "properties": {
      "mode": "Incremental",
      "templateLink": {
          "contentVersion": "1.0.0.0",
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
      },
      "parameters": {}
      }
    }
  ],
  "outputs": {
    "parentRG": {
      "type": "string",
      "value": "[concat('Parent resource group is ', resourceGroup().name)]"
    },
    "defaultScopeRG": {
      "type": "string",
      "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "innerScopeRG": {
      "type": "string",
      "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "linkedRG": {
      "type": "string",
      "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
    }
  }
}
```

Para testar o modelo anterior e ver os resultados, utilize powerShell ou Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

A saída do exemplo anterior é:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

A saída do exemplo anterior é:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Passos seguintes

* Para entender como definir parâmetros no seu modelo, consulte [Compreender a estrutura e a sintaxe dos modelos do Gestor](template-syntax.md)de Recursos Azure .
* Para obter dicas sobre a resolução de erros comuns de implementação, consulte erros comuns de [implantação do Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para obter informações sobre a implementação de um modelo que requer uma ficha SAS, consulte [Implementar modelo privado com token SAS](secure-template-with-sas-token.md).

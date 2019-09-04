---
title: Local do recurso do modelo de Azure Resource Manager
description: Descreve como definir o local do recurso em um modelo de Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: tomfitz
ms.openlocfilehash: 6af5d82766533ef7bbacab04381db9a37502923e
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294207"
---
# <a name="set-resource-location-in-resource-manager-template"></a>Definir local do recurso no modelo do Resource Manager

Ao implantar um modelo, você deve fornecer um local para cada recurso. O local não precisa ser o mesmo local que o local do grupo de recursos.

## <a name="get-available-locations"></a>Obter locais disponíveis

Diferentes tipos de recursos têm suporte em locais diferentes. Para obter os locais com suporte para um tipo de recurso, use Azure PowerShell ou CLI do Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Usar parâmetro de local

Para permitir a flexibilidade ao implantar seu modelo, use um parâmetro para especificar o local dos recursos. Defina o valor padrão do parâmetro como `resourceGroup().location`.

O exemplo a seguir mostra uma conta de armazenamento que é implantada em um local especificado como um parâmetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

* Para obter a lista completa de funções de modelo, consulte [Azure Resource Manager funções de modelo](resource-group-template-functions.md).
* Para obter mais informações sobre arquivos de modelo, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).

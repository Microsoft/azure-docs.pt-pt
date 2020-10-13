---
title: Localização do recurso do modelo
description: Descreve como definir a localização do recurso num modelo do Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: ''
ms.openlocfilehash: e1310c81d7b726e9be2fe9f38a0bb8d701035ba4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613701"
---
# <a name="set-resource-location-in-arm-template"></a>Definir localização de recursos no modelo ARM

Ao implementar um modelo Azure Resource Manager (ARM), deve fornecer uma localização para cada recurso. A localização não precisa de ser a mesma localização da localização do grupo de recursos.

## <a name="get-available-locations"></a>Obtenha locais disponíveis

Diferentes tipos de recursos são suportados em diferentes locais. Para obter os locais suportados para um tipo de recurso, utilize a Azure PowerShell ou a Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Use o parâmetro de localização

Para permitir a flexibilidade ao implementar o seu modelo, utilize um parâmetro para especificar a localização dos recursos. Desafine o valor predefinido do parâmetro para `resourceGroup().location` .

O exemplo a seguir mostra uma conta de armazenamento que é implantada num local especificado como parâmetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
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

## <a name="next-steps"></a>Passos seguintes

* Para obter a lista completa das funções do modelo, consulte as [funções do modelo do Gestor de Recursos Azure](template-functions.md).
* Para obter mais informações sobre ficheiros de modelos, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).

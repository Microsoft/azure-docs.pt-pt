---
title: Localização do recurso do modelo
description: Descreve como definir a localização dos recursos num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80156434"
---
# <a name="set-resource-location-in-arm-template"></a>Definir localização de recursos no modelo ARM

Ao implementar um modelo de Gestor de Recursos Azure (ARM), deve fornecer uma localização para cada recurso. A localização não precisa de ser a mesma localização que a localização do grupo de recursos.

## <a name="get-available-locations"></a>Obtenha locais disponíveis

Diferentes tipos de recursos são suportados em diferentes locais. Para obter os locais suportados para um tipo de recurso, utilize o Azure PowerShell ou o Azure CLI.

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

## <a name="use-location-parameter"></a>Utilizar parâmetro de localização

Para permitir flexibilidade ao implementar o seu modelo, utilize um parâmetro para especificar a localização dos recursos. Defina o valor padrão `resourceGroup().location`do parâmetro para .

O exemplo seguinte mostra uma conta de armazenamento que é implantada para um local especificado como parâmetro:

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

* Para obter a lista completa das funções do modelo, consulte as funções do modelo do Gestor de [Recursos do Azure](template-functions.md).
* Para obter mais informações sobre ficheiros de modelos, consulte [Compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).

---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/11/2019
ms.author: tomfitz
ms.openlocfilehash: 104bd13a0cf97a8605670adde479c2a2eeb29c15
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57786333"
---
Para marcar um recurso durante a implementação, adicione o `tags` elemento para o recurso estiver a implementar. Forneça o nome de etiqueta e o valor.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Aplicar um valor literal ao nome da etiqueta
O exemplo seguinte mostra uma conta de armazenamento com duas etiquetas (`Dept` e `Environment`) que estão definidas com valores literais:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Para definir uma etiqueta para um valor de datetime, utilize o [utcNow função](../articles/azure-resource-manager/resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Aplicar um objeto ao elemento da etiqueta
Pode definir um parâmetro de objeto que armazene várias etiquetas e aplicar esse objeto no elemento da etiqueta. Cada propriedade no objeto torna-se uma etiqueta separada para o recurso. O exemplo seguinte tem um parâmetro denominado `tagValues` que é aplicado no elemento da etiqueta.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagValues": {
      "type": "object",
      "defaultValue": {
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": "[parameters('tagValues')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    }
  ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Aplicar uma cadeia JSON ao nome da etiqueta

Para armazenar muitos valores numa única etiqueta, aplique uma cadeia JSON que represente os valores. Toda a cadeia JSON é armazenada como uma etiqueta que não pode exceder os 256 carateres. O exemplo seguinte tem uma única etiqueta com o nome `CostCenter` que contém vários valores de uma cadeia JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```
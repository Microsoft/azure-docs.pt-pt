---
title: Implementação condicional com modelos
description: Descreve como implantar um recurso condicionalmente num modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741179"
---
# <a name="conditional-deployment-in-arm-templates"></a>Implementação condicional nos modelos do ARM

Por vezes, é necessário implantar opcionalmente um recurso num modelo de Gestor de Recursos Azure (modelo ARM) ou ficheiro Bicep. Para os modelos JSON, utilize o `condition` elemento para especificar se o recurso é implantado. Para Bicep, utilize a `if` palavra-chave para especificar se o recurso está implantado. O valor da condição resolve-se a verdade ou falsa. Quando o valor é verdadeiro, o recurso é criado. Quando o valor é falso, o recurso não é criado. O valor só pode ser aplicado a todo o recurso.

> [!NOTE]
> A implantação condicional não se cascata para [os recursos infantis.](child-resource-name-type.md) Se pretender implantar condicionalmente um recurso e os seus recursos para crianças, deve aplicar a mesma condição a cada tipo de recurso.

## <a name="deploy-condition"></a>Implementar condição

Pode passar num valor de parâmetro que indique se um recurso é implantado. O exemplo seguinte implanta condicionalmente uma zona de DNS.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Para um exemplo mais complexo, consulte [o servidor lógico Azure SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="new-or-existing-resource"></a>Recurso novo ou existente

Pode utilizar a implementação condicional para criar um novo recurso ou utilizar um existente. O exemplo a seguir mostra como implantar uma nova conta de armazenamento ou utilizar uma conta de armazenamento existente.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

Quando o parâmetro `newOrExisting` é definido para **novo,** a condição avalia a verdade. A conta de armazenamento está implantada. No entanto, quando `newOrExisting` está definido para **existir,** a condição avalia-se a falsa e a conta de armazenamento não é implantada.

Para obter um modelo de exemplo completo que utilize o `condition` elemento, consulte [VM com uma rede virtual nova ou existente, armazenamento e IP público.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)

## <a name="runtime-functions"></a>Funções de tempo de execução

Se utilizar uma função [de referência](template-functions-resource.md#reference) ou [lista](template-functions-resource.md#list) com um recurso que esteja implantado condicionalmente, a função é avaliada mesmo que o recurso não seja implantado. Obtém-se um erro se a função se referir a um recurso que não existe.

Utilize a função [se](template-functions-logical.md#if) funcionar para se certificar de que a função só é avaliada para as condições quando o recurso é implantado. Consulte a [função se](template-functions-logical.md#if) for para um modelo de amostra que utiliza `if` e com um recurso implantado `reference` condicionalmente.

Define um [recurso tão dependente](define-resource-dependency.md) de um recurso condicional exatamente como qualquer outro recurso. Quando um recurso condicional não é implantado, o Azure Resource Manager remove-o automaticamente das dependências necessárias.

## <a name="complete-mode"></a>Modo completo

Se implementar um modelo com [o modo completo](deployment-modes.md) e um recurso não for implementado porque avalia em `condition` falso, o resultado depende da versão API REST que utiliza para implementar o modelo. Se utilizar uma versão anterior a 2019-05-10, o recurso **não é eliminado**. Com 2019-05-10 ou posteriormente, o recurso **é eliminado.** As versões mais recentes do Azure PowerShell e do Azure CLI apagam o recurso quando a condição é falsa.

## <a name="next-steps"></a>Passos seguintes

* Para um módulo Microsoft Learn que cubra a implementação condicional, consulte [Gerir implementações complexas em nuvem utilizando funcionalidades avançadas do modelo ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Para recomendações sobre a criação de modelos, consulte [as melhores práticas do modelo ARM](template-best-practices.md).
* Para criar múltiplas instâncias de um recurso, consulte [a iteração de recursos nos modelos ARM](copy-resources.md).

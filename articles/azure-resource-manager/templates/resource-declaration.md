---
title: Declarar recursos em modelos
description: Descreve como declarar recursos para implantar num modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746659"
---
# <a name="resource-declaration-in-arm-templates"></a>Declaração de recursos em modelos ARM

Para implementar um recurso através de um modelo de Gestor de Recursos Azure (modelo ARM), adicione uma declaração de recurso. Utilize a `resources` matriz para o modelo JSON ou a `resource` palavra-chave para Bicep.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Definir tipo de recurso e versão

Ao adicionar um recurso ao seu modelo, comece por definir o tipo de recurso e a versão API. Estes valores determinam as outras propriedades disponíveis para o recurso.

O exemplo a seguir mostra como definir o tipo de recurso e a versão API para uma conta de armazenamento. O exemplo não mostra a declaração completa de recursos.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Para Bicep, define um nome simbólico para o recurso. No exemplo anterior, o nome simbólico é `sa` . Pode usar qualquer valor para o nome simbólico, mas não pode ser o mesmo que outro recurso, parâmetro ou variável no modelo. O nome simbólico não é o mesmo que o nome do recurso. Utilize o nome simbólico para facilmente referenciar o recurso noutras partes do seu modelo.

## <a name="set-resource-name"></a>Definir nome de recurso

Cada recurso tem um nome. Ao definir o nome do recurso, preste atenção às [regras e restrições para nomes de recursos](../management/resource-name-rules.md).

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Definir localização

Muitos recursos requerem uma localização. Pode determinar se o recurso precisa de uma localização através do intellisense ou [da referência do modelo.](/azure/templates/) O exemplo a seguir adiciona um parâmetro de localização que é usado para a conta de armazenamento.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

Para obter mais informações, consulte [a localização do recurso no modelo ARM](resource-location.md).

## <a name="set-tags"></a>Definir tags

Pode aplicar etiquetas a um recurso durante a implantação. As etiquetas ajudam-no a organizar logicamente os seus recursos implantados. Por exemplo, as diferentes formas de especificar as etiquetas, consulte as [etiquetas do modelo ARM](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Definir propriedades específicas de recursos

As propriedades anteriores são genéricas para a maioria dos tipos de recursos. Depois de definir estes valores, precisa de definir as propriedades específicas do tipo de recurso que está a implementar.

Use intellisense ou [referência do modelo](/azure/templates/) para determinar quais as propriedades disponíveis e quais são necessárias. O exemplo a seguir define as propriedades restantes para uma conta de armazenamento.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
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
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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

## <a name="next-steps"></a>Passos seguintes

* Para implementar um recurso condicionalmente, consulte [a implementação condicional nos modelos ARM](conditional-resource-deployment.md).
* Para definir as dependências de recursos, consulte [definir a ordem para a implantação de recursos em modelos ARM](define-resource-dependency.md).

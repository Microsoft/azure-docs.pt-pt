---
title: Funções definidas pelo usuário em modelos
description: Descreve como definir e usar funções definidas pelo usuário em um modelo de Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: e2e68c6f6b9fc9eb40d282d87572ce1e805a11a8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483807"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funções definidas pelo usuário no modelo Azure Resource Manager

Em seu modelo, você pode criar suas próprias funções. Essas funções estão disponíveis para uso em seu modelo. As funções definidas pelo usuário são separadas das [funções de modelo padrão](template-functions.md) que estão automaticamente disponíveis no seu modelo. Crie suas próprias funções quando tiver expressões complicadas que são usadas repetidamente em seu modelo.

Este artigo descreve como adicionar funções definidas pelo usuário em seu modelo de Azure Resource Manager.

## <a name="define-the-function"></a>Definir a função

Suas funções exigem um valor de namespace para evitar conflitos de nomenclatura com funções de modelo. O exemplo a seguir mostra uma função que retorna um nome de conta de armazenamento:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Usar a função

O exemplo a seguir mostra como chamar sua função.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>Limitações

Ao definir uma função de usuário, há algumas restrições:

* A função não pode acessar variáveis.
* A função só pode usar parâmetros que são definidos na função. Quando você usa a função [Parameters](template-functions-deployment.md#parameters) em uma função definida pelo usuário, você está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo usuário.
* A função não pode usar a função de [referência](template-functions-resource.md#reference) ou qualquer uma das funções de [lista](template-functions-resource.md#list) .
* Os parâmetros da função não podem ter valores padrão.


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as propriedades disponíveis para funções definidas pelo usuário, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](template-syntax.md).
* Para obter uma lista das funções de modelo disponíveis, consulte [Azure Resource Manager funções de modelo](template-functions.md).

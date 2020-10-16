---
title: Funções definidas pelo utilizador em modelos
description: Descreve como definir e usar funções definidas pelo utilizador num modelo de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 69f4e98d389cc8dbe5cd3f4b628189676c501106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84672940"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Funções definidas pelo utilizador no modelo do Gestor de Recursos Azure

Dentro do seu modelo, pode criar as suas próprias funções. Estas funções estão disponíveis para utilização no seu modelo. As funções definidas pelo utilizador são separadas das [funções padrão](template-functions.md) do modelo que estão automaticamente disponíveis dentro do seu modelo. Crie as suas próprias funções quando tiver expressões complicadas que são usadas repetidamente no seu modelo.

Este artigo descreve como adicionar funções definidas pelo utilizador no seu modelo de Gestor de Recursos Azure.

## <a name="define-the-function"></a>Definir a função

As suas funções requerem um valor de espaço de nome para evitar o nome de conflitos com funções de modelo. O exemplo a seguir mostra uma função que devolve um nome único:

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

## <a name="use-the-function"></a>Use a função

O exemplo a seguir mostra um modelo que inclui uma função definida pelo utilizador. Usa essa função para obter um nome único para uma conta de armazenamento. O modelo tem um parâmetro chamado **armazenamentoNamePrefixo** que passa como um parâmetro para a função.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
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
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Limitações

Ao definir uma função de utilizador, existem algumas restrições:

* A função não pode aceder a variáveis.
* A função só pode utilizar parâmetros definidos na função. Quando utiliza a função de [parâmetros](template-functions-deployment.md#parameters) dentro de uma função definida pelo utilizador, está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo utilizador.
* A função não pode utilizar a função [de referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista.](template-functions-resource.md#list)
* Os parâmetros para a função não podem ter valores predefinidos.


## <a name="next-steps"></a>Passos seguintes

* Para conhecer as propriedades disponíveis para funções definidas pelo utilizador, consulte [a estrutura e sintaxe dos modelos do Gestor de Recursos Azure](template-syntax.md).
* Para obter uma lista das funções disponíveis do modelo, consulte as [funções do modelo do Gestor de Recursos Azure](template-functions.md).

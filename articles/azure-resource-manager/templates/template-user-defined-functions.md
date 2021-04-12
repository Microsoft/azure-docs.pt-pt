---
title: Funções definidas pelo utilizador em modelos
description: Descreve como definir e utilizar funções definidas pelo utilizador num modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 9c7480958e6315c8aea1fd8d12613bcf9d606723
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379629"
---
# <a name="user-defined-functions-in-arm-template"></a>Funções definidas pelo utilizador no modelo ARM

Dentro do seu modelo, pode criar as suas próprias funções. Estas funções estão disponíveis para utilização no seu modelo. As funções definidas pelo utilizador são separadas das [funções padrão](template-functions.md) do modelo que estão automaticamente disponíveis dentro do seu modelo. Crie as suas próprias funções quando tiver expressões complicadas que são usadas repetidamente no seu modelo.

Este artigo descreve como adicionar funções definidas pelo utilizador no seu modelo de Gestor de Recursos Azure (modelo ARM).

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

O exemplo a seguir mostra um modelo que inclui uma função definida pelo utilizador para obter um nome único para uma conta de armazenamento. O modelo tem um parâmetro nomeado `storageNamePrefix` que é passado como um parâmetro para a função.

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

Durante a implementação, o `storageNamePrefix` parâmetro é passado para a função:

* O modelo define um parâmetro chamado `storageNamePrefix` .
* A função `namePrefix` utiliza-se porque só é possível utilizar parâmetros definidos na função. Para mais informações, consulte [As Limitações.](#limitations)
* Na secção do `resources` modelo, o `name` elemento utiliza a função e passa o valor `storageNamePrefix` para a função `namePrefix` .

## <a name="limitations"></a>Limitações

Ao definir uma função de utilizador, existem algumas restrições:

* A função não pode aceder a variáveis.
* A função só pode utilizar parâmetros definidos na função. Quando utiliza a função de [parâmetros](template-functions-deployment.md#parameters) dentro de uma função definida pelo utilizador, está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo utilizador.
* A função não pode utilizar a função [de referência](template-functions-resource.md#reference) ou qualquer uma das funções da [lista.](template-functions-resource.md#list)
* Os parâmetros para a função não podem ter valores predefinidos.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre as propriedades disponíveis para funções definidas pelo utilizador, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
* Para obter uma lista das funções do modelo disponíveis, consulte as [funções do modelo ARM](template-functions.md).

---
title: Elemento UI do tetor de recursos
description: Descreve o elemento UI microsoft.Solutions.ResourceSelector uI para o portal Azure. Usado para obter uma lista de recursos existentes.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099058"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Microsoft.Solutions.ResourceSelector UI elemento

O ResourceSelector permite que os utilizadores selecionem um recurso existente a partir de uma subscrição.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Solutions.ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>Esquema

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>Observações

Na `resourceType` propriedade, forneça o espaço de nome do fornecedor de recursos e o nome do tipo de recurso para o recurso que deseja mostrar na lista.

A `filter` propriedade restringe as opções disponíveis para os recursos. Pode restringir os resultados por localização ou subscrição. Para mostrar apenas recursos que correspondam à seleção no básico, use `onBasics` . Para mostrar todos os recursos, use `all` . O valor predefinido é `all`.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

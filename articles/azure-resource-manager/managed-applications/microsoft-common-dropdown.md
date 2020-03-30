---
title: Elemento UI dropdown
description: Descreve o elemento Microsoft.Common.DropDown UI para o portal Azure. Utilize para selecionar as opções disponíveis ao implementar uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652388"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI

Um controlo de seleção com uma lista de abandono.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
"two"
```

## <a name="remarks"></a>Observações

- A etiqueta `constraints.allowedValues` para é o texto de exibição para um item, e o seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor predefinido `constraints.allowedValues`deve ser um rótulo presente em . Se não especificado, o `constraints.allowedValues` primeiro item é selecionado. O valor predefinido é **nulo.**
- `constraints.allowedValues`deve ter pelo menos um item.
- Para imitar um valor que não seja necessário, adicione `""` um item `constraints.allowedValues`com um rótulo e valor de (corda vazia) a .

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

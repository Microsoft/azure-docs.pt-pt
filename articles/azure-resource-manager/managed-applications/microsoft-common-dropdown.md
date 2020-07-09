---
title: Elemento de UI dropdown
description: Descreve o elemento UI Microsoft.Common.DropDown para o portal Azure. Utilize para selecionar as opções disponíveis ao implementar uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652388"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI elemento

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

- O rótulo para `constraints.allowedValues` é o texto do visor de um item, e o seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor predefinido deve ser um rótulo presente em `constraints.allowedValues` . Se não for especificado, o primeiro item `constraints.allowedValues` é selecionado. O valor por defeito é **nulo.**
- `constraints.allowedValues`deve ter pelo menos um item.
- Para imitar um valor que não está a ser necessário, adicione um item com uma etiqueta e valor de `""` (corda vazia) a `constraints.allowedValues` .

## <a name="next-steps"></a>Próximos passos

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

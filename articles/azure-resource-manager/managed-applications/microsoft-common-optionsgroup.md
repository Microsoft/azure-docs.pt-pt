---
title: Elemento UI do OptionsGroup
description: Descreve o elemento Microsoft.Common.OptionsGroup UI para o portal Azure. Permite que os utilizadores selecionem a partir das opções disponíveis ao implementar uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652349"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI

Um controlo de seleção com uma fileira de opções disponíveis.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
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
- Se especificado, o valor predefinido `constraints.allowedValues`deve ser um rótulo presente em . Se não especificado, o `constraints.allowedValues` primeiro item é selecionado por padrão. O valor predefinido é **nulo.**
- `constraints.allowedValues`deve ter pelo menos um item.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

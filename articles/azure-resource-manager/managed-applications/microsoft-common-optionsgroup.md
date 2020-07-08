---
title: OpçõesGrupo elemento UI
description: Descreve o elemento UI do Microsoft.Common.OptionsGroup para o portal Azure. Permite que os utilizadores selecionem entre as opções disponíveis ao implementar uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652349"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI elemento

Um controlo de seleção com uma linha de opções disponíveis.

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

- O rótulo para `constraints.allowedValues` é o texto do visor de um item, e o seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor predefinido deve ser um rótulo presente em `constraints.allowedValues` . Se não for especificado, o primeiro item `constraints.allowedValues` é selecionado por predefinição. O valor por defeito é **nulo.**
- `constraints.allowedValues`deve ter pelo menos um item.

## <a name="next-steps"></a>Próximos passos

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

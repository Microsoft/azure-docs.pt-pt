---
title: Elemento UI da secção
description: Descreve o elemento UI da Secção Microsoft.Common para o portal Azure. Utilize elementos de grupo no portal para implantar aplicações geridas.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652258"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI elemento

Um controlo que agrupe um ou mais elementos sob um título.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Observações

- `elements`deve ter pelo menos um elemento, e pode ter todos os tipos de elementos, exceto `Microsoft.Common.Section` .
- Este elemento não suporta a `toolTip` propriedade.

## <a name="sample-output"></a>Resultado da amostra
Para aceder aos valores de saída dos `elements` elementos, utilize as [funções básicas()](create-uidefinition-functions.md#basics) ou [passos()](create-uidefinition-functions.md#steps) e a notação de pontos:

```json
steps('configuration').section1.text1
```

Os elementos do tipo não têm valores de `Microsoft.Common.Section` saída em si.

## <a name="next-steps"></a>Próximos passos

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

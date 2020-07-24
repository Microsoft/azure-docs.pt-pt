---
title: Elemento UI da secção
description: Descreve o elemento UI da Secção Microsoft.Common para o portal Azure. Utilize elementos de grupo no portal para implantar aplicações geridas.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87063986"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI elemento

Um controlo que agrupe um ou mais elementos sob um título.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

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
Para aceder aos valores de saída dos `elements` elementos, utilize as [funções básicas()](create-ui-definition-referencing-functions.md#basics) ou [passos()](create-ui-definition-referencing-functions.md#steps) e a notação de pontos:

```json
steps('configuration').section1.text1
```

Os elementos do tipo não têm valores de `Microsoft.Common.Section` saída em si.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

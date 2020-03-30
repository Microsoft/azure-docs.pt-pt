---
title: Elemento da secção UI
description: Descreve o elemento Microsoft.Common.Section UI para o portal Azure. Utilizar para agrupar elementos no portal para a implementação de aplicações geridas.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652258"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI elemento

Um controlo que agrupa um ou mais elementos sob um título.

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

- `elements`deve ter pelo menos um elemento, e `Microsoft.Common.Section`pode ter todos os tipos de elementos, exceto .
- Este elemento não suporta `toolTip` a propriedade.

## <a name="sample-output"></a>Resultado da amostra
Para aceder aos valores `elements`de saída dos elementos, utilize as funções [básicas()](create-uidefinition-functions.md#basics) ou [passos](create-uidefinition-functions.md#steps) e notação de pontos:

```json
steps('configuration').section1.text1
```

Elementos do `Microsoft.Common.Section` tipo não têm valores de saída.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

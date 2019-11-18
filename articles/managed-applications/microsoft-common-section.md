---
title: Elemento de interface do usuário da seção do Azure | Microsoft Docs
description: Descreve o elemento da interface do usuário Microsoft. Common. Section para portal do Azure. Use o para agrupar elementos no portal para a implantação de aplicativos gerenciados.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: fd2c1105078b918043791fd0f18395409bb32f7c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151706"
---
# <a name="microsoftcommonsection-ui-element"></a>Elemento de interface do usuário Microsoft. Common. Section

Um controle que agrupa um ou mais elementos em um título.

## <a name="ui-sample"></a>Exemplo de interface do usuário

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

- `elements` deve ter pelo menos um elemento e pode ter todos os tipos de elemento, exceto `Microsoft.Common.Section`.
- Este elemento não dá suporte à propriedade `toolTip`.

## <a name="sample-output"></a>Resultado da amostra
Para acessar os valores de saída dos elementos no `elements`, use as funções [Basics ()](create-uidefinition-functions.md#basics) ou [Steps ()](create-uidefinition-functions.md#steps) e a notação de ponto:

```json
steps('configuration').section1.text1
```

Elementos do tipo `Microsoft.Common.Section` não têm nenhum valor de saída.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

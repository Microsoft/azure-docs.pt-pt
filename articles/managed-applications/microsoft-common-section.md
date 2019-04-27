---
title: Elemento de interface do Usuário de secção do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Common.Section para o portal do Azure.
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
ms.openlocfilehash: c89b45dd4d8e6c2964f3d2bcbb6c3cef445c79e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251887"
---
# <a name="microsoftcommonsection-ui-element"></a>Elemento de interface do Usuário de Microsoft.Common.Section
Um controlo que agrupa uma ou mais elementos sob um cabeçalho.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
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
- `elements` tem de ter pelo menos um elemento e pode ter todos os tipos de elementos, exceto `Microsoft.Common.Section`.
- Este elemento não suporta o `toolTip` propriedade.

## <a name="sample-output"></a>Resultado da amostra
Para acessar os valores de saída de elementos na `elements`, utilize o [basics()](create-uidefinition-functions.md#basics) ou [steps()](create-uidefinition-functions.md#steps) funções e a notação de ponto:

```json
steps('configuration').section1.text1
```

Elementos do tipo `Microsoft.Common.Section` não ter nenhum aos valores de saída.

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

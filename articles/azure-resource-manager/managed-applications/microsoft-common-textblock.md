---
title: Elemento UI do TextBlock
description: Descreve o elemento Microsoft.Common.TextBlock UI para o portal Azure. Utilize para adicionar texto à interface.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652219"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock UI

Um controlo que pode ser usado para adicionar texto à interface do portal.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

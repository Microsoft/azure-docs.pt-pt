---
title: Elemento de interface do usuário do TextBlock
description: Descreve o elemento de interface do usuário Microsoft. Common. TextBlock para portal do Azure. Use para adicionar texto à interface.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652219"
---
# <a name="microsoftcommontextblock-ui-element"></a>Elemento de interface do usuário Microsoft. Common. TextBlock

Um controle que pode ser usado para adicionar texto à interface do Portal.

## <a name="ui-sample"></a>Exemplo de interface do usuário

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

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

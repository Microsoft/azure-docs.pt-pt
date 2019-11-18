---
title: Elemento de interface do usuário caixa do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. Common. caixa para portal do Azure. Use para adicionar texto ou avisos ao implantar o aplicativo gerenciado.
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
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 3a72aaaa15b55b2f0fbc0a227c36a4b2f624d43b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151320"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Elemento de interface do usuário Microsoft. Common. caixa

Um controle que adiciona uma caixa de informações. A caixa contém texto ou avisos importantes que ajudam os usuários a entender os valores que eles estão fornecendo. Ele também pode vincular a um URI para obter mais informações.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Esquema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Observações

* Por `icon`, use **nenhum**, **informações**, **aviso**ou **erro**.
* A propriedade `uri` é opcional.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

---
title: Elemento de interface do Usuário de caixa de informações do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Common.TextBlock para o portal do Azure.
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
ms.openlocfilehash: 2330197b4512dfdd72de3529145103b644594e25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711209"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Elemento de interface do Usuário de Microsoft.Common.InfoBox
Um controle que adiciona uma caixa de informações. A caixa contém texto importante ou avisos que ajudam os utilizadores a compreender os valores que precisam. Também pode ligar a um URI para obter mais informações.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
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

## <a name="remarks"></a>Observações

* Para `icon`, utilize **None**, **informações**, **aviso**, ou **erro**.
* O `uri` propriedade é opcional.

## <a name="sample-output"></a>Resultado da amostra

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

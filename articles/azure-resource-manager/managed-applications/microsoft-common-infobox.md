---
title: Elemento UI infoBox
description: Descreve o elemento UI microsoft.common.infobox para o portal Azure. Utilize para adicionar texto ou avisos ao implementar uma aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652479"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox UI elemento

Um controlo que adiciona uma caixa de informação. A caixa contém textos importantes ou avisos que ajudam os utilizadores a entender os valores que estão a fornecer. Também pode ligar-se a um URI para mais informações.

## <a name="ui-sample"></a>Amostra de UI

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

* Para `icon` , usar **Nenhum**, **Informação**, **Aviso**ou **Erro**.
* A `uri` propriedade é opcional.

## <a name="next-steps"></a>Próximos passos

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

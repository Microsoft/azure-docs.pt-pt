---
title: Elemento de UI de slider
description: Descreve o elemento UI Microsoft.Common.Slider para o portal Azure. Permite que os utilizadores estabeleçam um valor a partir de um leque de opções.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098533"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft.Common.Slider UI elemento

O controlo Slider permite que os utilizadores selecionem a partir de uma gama de valores permitidos.

## <a name="ui-sample"></a>Amostra de UI

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft.Common.Slider":::

## <a name="schema"></a>Esquema

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
26
```

## <a name="remarks"></a>Observações

- Os `min` `max` valores são necessários. Eles definem os pontos de partida e de fim para o slider.
- A `showStepMarkers` propriedade não é verdadeira. Os marcadores de passo só são mostrados quando o intervalo de min a máximo é de 100 ou menos.


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

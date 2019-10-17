---
title: Elemento de interface do usuário do Azure Options | Microsoft Docs
description: Descreve o elemento Microsoft. Common. Options da interface do usuário para portal do Azure. Permite que os usuários selecionem as opções disponíveis ao implantar um aplicativo gerenciado.
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
ms.openlocfilehash: 7bec506575f1c526e59487edb67349cdf862f1a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331674"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elemento de interface do usuário Microsoft. Common. Options
Um controle de seleção com uma linha de opções disponíveis.

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft. Common. Options](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- O rótulo de `constraints.allowedValues` é o texto de exibição de um item e seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor padrão deve ser um rótulo presente no `constraints.allowedValues`. Se não for especificado, o primeiro item em `constraints.allowedValues` será selecionado por padrão. O valor padrão é **NULL**.
- `constraints.allowedValues` deve ter pelo menos um item.

## <a name="sample-output"></a>Resultado da amostra
```json
"two"
```

## <a name="next-steps"></a>Passos seguintes
* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

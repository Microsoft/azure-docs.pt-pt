---
title: Elemento de interface do usuário da lista suspensa do Azure | Microsoft Docs
description: Descreve o elemento Microsoft. Common. DropDown da interface do usuário para portal do Azure. Use para selecionar as opções disponíveis ao implantar um aplicativo gerenciado.
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
ms.openlocfilehash: e1db91362d17d9100199f0b30119f8024f00a903
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151332"
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento de interface do usuário Microsoft. Common. DropDown

Um controle de seleção com uma lista suspensa.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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

## <a name="sample-output"></a>Resultado da amostra

```json
"two"
```

## <a name="remarks"></a>Observações

- O rótulo de `constraints.allowedValues` é o texto de exibição de um item e seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor padrão deve ser um rótulo presente no `constraints.allowedValues`. Se não for especificado, o primeiro item em `constraints.allowedValues` será selecionado. O valor padrão é **NULL**.
- `constraints.allowedValues` deve ter pelo menos um item.
- Para emular um valor que não está sendo necessário, adicione um item com um rótulo e um valor de `""` (cadeia de caracteres vazia) a `constraints.allowedValues`.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

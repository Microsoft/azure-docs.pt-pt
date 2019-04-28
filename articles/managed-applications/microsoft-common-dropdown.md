---
title: Elemento de interface do Usuário de lista pendente do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Common.DropDown para o portal do Azure.
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
ms.openlocfilehash: e78fa419b067c0bad48229dcfd8d4e986fc16903
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62117306"
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento de interface do Usuário de Microsoft.Common.DropDown
Um controlo de seleção com uma lista suspensa.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
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

## <a name="remarks"></a>Observações

- A etiqueta de `constraints.allowedValues` é o texto de exibição para um item, e seu valor é o valor de saída do elemento quando selecionado.
- Se for especificado, o valor predefinido tem de ser uma etiqueta presente no `constraints.allowedValues`. Se não for especificado, o primeiro item na `constraints.allowedValues` está selecionada. O valor predefinido é **nulo**.
- `constraints.allowedValues` tem de ter pelo menos um item.
- Para emular um valor não sendo necessário, adicione um item com uma etiqueta e valor de `""` (cadeia de caracteres vazia) para `constraints.allowedValues`.

## <a name="sample-output"></a>Resultado da amostra
```json
"two"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

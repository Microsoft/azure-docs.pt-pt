---
title: Elemento de interface do Usuário de caixa de texto do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Common.TextBox para o portal do Azure.
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
ms.openlocfilehash: b06e8b49efe8b6de720fa9bb819d4720e4f80fb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61044551"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento de interface do Usuário de Microsoft.Common.TextBox
Um controlo que pode ser usado para editar o texto formatado.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- Se `constraints.required` está definido como **true**, em seguida, a caixa de texto tem de ter um valor para validar com êxito. O valor predefinido é **false**.
- `constraints.regex` é um padrão de expressão regular de JavaScript. Se for especificado, em seguida, valor da caixa de texto tem de corresponder ao padrão para validar com êxito. O valor predefinido é **nulo**.
- `constraints.validationMessage` é uma cadeia a apresentar quando o valor da caixa de texto falha na validação. Se não for especificado, em seguida, são utilizadas as mensagens de validação incorporadas a caixa de texto. O valor predefinido é **nulo**.
- É possível especificar um valor para `constraints.regex` quando `constraints.required` está definida como **falso**. Neste cenário, um valor não a é necessário para a caixa de texto validar com êxito. Se for especificado, tem de corresponder o padrão de expressão regular.

## <a name="sample-output"></a>Resultado da amostra

```json
"my text value"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

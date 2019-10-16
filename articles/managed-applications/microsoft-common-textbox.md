---
title: Elemento de IU da caixa de texto do Azure | Microsoft Docs
description: Descreve o elemento Microsoft. Common. TextBox da interface do usuário para portal do Azure. Use para adicionar texto não formatado.
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
ms.openlocfilehash: b72bfcf06f4c7e256dd227e4edf781c4500c7b19
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331609"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento de interface do usuário Microsoft. Common. TextBox
Um controle que pode ser usado para editar texto não formatado.

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft. Common. TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

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
- Se `constraints.required` for definido como **true**, a caixa de texto deverá ter um valor para validar com êxito. O valor padrão é **false**.
- `constraints.regex` é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com êxito. O valor padrão é **NULL**.
- `constraints.validationMessage` é uma cadeia de caracteres a ser exibida quando o valor da caixa de texto falha na validação. Se não for especificado, as mensagens de validação internas da caixa de texto serão usadas. O valor padrão é **NULL**.
- É possível especificar um valor para `constraints.regex` quando `constraints.required` for definido como **false**. Nesse cenário, um valor não é necessário para que a caixa de texto seja validada com êxito. Se um for especificado, ele deverá corresponder ao padrão de expressão regular.

## <a name="sample-output"></a>Resultado da amostra

```json
"my text value"
```

## <a name="next-steps"></a>Passos seguintes
* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

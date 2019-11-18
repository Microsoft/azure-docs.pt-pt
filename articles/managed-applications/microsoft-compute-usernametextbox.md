---
title: Elemento de interface do usuário UserNameTextBox do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. COMPUTE. UserNameTextBox para portal do Azure. Permite que os usuários forneçam nomes de usuário do Windows ou Linux.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 47dff5fb23da7bf816e46cf8e3e5696faadc7409
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151502"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Elemento de interface do usuário Microsoft. COMPUTE. UserNameTextBox

Um controle de caixa de texto com validação interna para nomes de usuário do Windows e do Linux.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
"Example name"
```

## <a name="remarks"></a>Observações

- Se `constraints.required` for definido como **true**, a caixa de texto deverá ter um valor para validar com êxito. O valor padrão é **true**.
- `osPlatform` deve ser especificado e pode ser **Windows** ou **Linux**.
- `constraints.regex` é um padrão de expressão regular do JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com êxito. O valor padrão é **NULL**.
- `constraints.validationMessage` é uma cadeia de caracteres a ser exibida quando o valor da caixa de texto falha na validação especificada por `constraints.regex`. Se não for especificado, as mensagens de validação internas da caixa de texto serão usadas. O valor padrão é **NULL**.
- Esse elemento tem validação interna baseada no valor especificado para `osPlatform`. A validação interna pode ser usada junto com uma expressão regular personalizada. Se um valor para `constraints.regex` for especificado, as validações internas e personalizadas serão disparadas.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

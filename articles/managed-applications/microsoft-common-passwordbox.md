---
title: Elemento de interface do usuário PasswordBox do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. Common. PasswordBox para portal do Azure. Permite que os usuários forneçam um valor secreto ao implantar aplicativos gerenciados.
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
ms.openlocfilehash: ab51a4096745c2930199685ac678638a956f21e0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151544"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Elemento de interface do usuário Microsoft. Common. PasswordBox

Um controle que pode ser usado para fornecer e confirmar uma senha.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Resultado da amostra

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Observações

- Este elemento não dá suporte à propriedade `defaultValue`.
- Para obter detalhes de implementação de `constraints`, consulte [Microsoft. Common. TextBox](microsoft-common-textbox.md).
- Se `options.hideConfirmation` for definido como **true**, a segunda caixa de texto para confirmar a senha do usuário ficará oculta. O valor predefinido é **false**.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à criação de definições de interface do usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos da interface do usuário, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

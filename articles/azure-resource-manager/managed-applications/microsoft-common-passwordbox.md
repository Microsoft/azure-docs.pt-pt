---
title: Elemento UI passwordBox
description: Descreve o elemento Microsoft.Common.PasswordBox UI para o portal Azure. Permite que os utilizadores forneçam um valor secreto ao implementarem aplicações geridas.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652310"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI

Um controlo que pode ser usado para fornecer e confirmar uma senha.

## <a name="ui-sample"></a>Amostra de UI

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

- Este elemento não suporta `defaultValue` a propriedade.
- Para obter `constraints`detalhes de implementação de , consulte [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Se `options.hideConfirmation` for definido como **verdadeiro,** a segunda caixa de texto para confirmar a palavra-passe do utilizador está escondida. O valor predefinido é **falso**.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

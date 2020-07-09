---
title: Elemento UI da PasswordBox
description: Descreve o elemento UI Microsoft.Common.PasswordBox para o portal Azure. Permite que os utilizadores forneçam um valor secreto ao implementar aplicações geridas.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652310"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox uI elemento

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

- Este elemento não suporta a `defaultValue` propriedade.
- Para obter detalhes de implementação `constraints` de , consulte [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Se `options.hideConfirmation` estiver definido como **verdadeiro,** a segunda caixa de texto para confirmar a palavra-passe do utilizador está escondida. O valor predefinido é **falso**.

## <a name="next-steps"></a>Próximos passos

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

---
title: Elemento de interface do Usuário de PasswordBox do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Common.PasswordBox para o portal do Azure.
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
ms.openlocfilehash: 944f59da680c3a058a3cd245cca48d903e44ab87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64710936"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Elemento de interface do Usuário de Microsoft.Common.PasswordBox
Um controlo que pode ser utilizado para fornecer e confirme uma palavra-passe.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
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
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Observações
- Este elemento não suporta o `defaultValue` propriedade.
- Para obter detalhes de implementação do `constraints`, consulte [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Se `options.hideConfirmation` está definido como **true**, a segunda caixa de texto para confirmar a palavra-passe do utilizador está oculto. O valor predefinido é **false**.

## <a name="sample-output"></a>Resultado da amostra
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

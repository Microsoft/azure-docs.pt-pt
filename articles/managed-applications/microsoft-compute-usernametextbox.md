---
title: Elemento de interface do Usuário de Ctl00$contentplaceholder1$usernametextbox do Azure | Documentos da Microsoft
description: Descreve o elemento de interface do Usuário de Microsoft.Compute.UserNameTextBox para o portal do Azure.
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
ms.openlocfilehash: 88ab13329a719ba1e1b8a7b5fba2f7a2d381eca2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700848"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Elemento de interface do Usuário de Microsoft.Compute.UserNameTextBox
Um controle de caixa de texto com validação incorporada para os nomes de utilizador do Windows e Linux.

## <a name="ui-sample"></a>Exemplo de interface do Usuário
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

## <a name="remarks"></a>Observações
- Se `constraints.required` está definido como **true**, em seguida, a caixa de texto tem de ter um valor para validar com êxito. O valor predefinido é **true**.
- `osPlatform` tem de ser especificado e pode ser uma **Windows** ou **Linux**.
- `constraints.regex` é um padrão de expressão regular de JavaScript. Se for especificado, em seguida, valor da caixa de texto tem de corresponder ao padrão para validar com êxito. O valor predefinido é **nulo**.
- `constraints.validationMessage` é uma cadeia a apresentar quando o valor da caixa de texto falha a validação especificada pelo `constraints.regex`. Se não for especificado, em seguida, são utilizadas as mensagens de validação incorporadas a caixa de texto. O valor predefinido é **nulo**.
- Este elemento tem de validação incorporada com base no valor especificado para `osPlatform`. A validação interna pode ser utilizada juntamente com uma expressão regular personalizada. Se um valor para `constraints.regex` for especificada, em seguida, são acionadas validações incorporadas e personalizadas.

## <a name="sample-output"></a>Resultado da amostra
```json
"Example name"
```

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma introdução à criação de definições de interface do Usuário, consulte [introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do Usuário, consulte [CreateUiDefinition elementos](create-uidefinition-elements.md).

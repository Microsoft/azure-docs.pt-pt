---
title: UserNameTextBox UI
description: Descreve o elemento Microsoft.Compute.UserNameTextBox UI para o portal Azure. Permite que os utilizadores forneçam nomes de utilizadores windows ou Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651907"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.userNameTextBox UI

Um controlo de caixa de texto com validação incorporada para nomes de utilizadores Windows e Linux.

## <a name="ui-sample"></a>Amostra de UI

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

- Se `constraints.required` for definido como **verdadeiro,** então a caixa de texto deve ter um valor para validar com sucesso. O valor padrão é **verdadeiro.**
- `osPlatform`deve ser especificado e pode ser **Windows** ou **Linux**.
- `constraints.regex`é um padrão de expressão regular JavaScript. Se especificado, então o valor da caixa de texto deve corresponder ao padrão para validar com sucesso. O valor predefinido é **nulo.**
- `constraints.validationMessage`é uma corda para exibir quando o valor da caixa `constraints.regex`de texto falha na validação especificada por . Se não especificadas, as mensagens de validação incorporadas da caixa de texto são utilizadas. O valor predefinido é **nulo.**
- Este elemento tem validação incorporada que se baseia `osPlatform`no valor especificado para . A validação incorporada pode ser usada juntamente com uma expressão regular personalizada. Se `constraints.regex` for especificado um valor, as validações incorporadas e personalizadas são desencadeadas.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

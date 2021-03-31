---
title: Elemento UI do UserNameTextBox
description: Descreve o elemento microsoft.compute.userNameTextBox UI para o portal Azure. Permite que os utilizadores forneçam nomes de utilizador Windows ou Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 82478f322e1df22bde50769b90f0424140920e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063567"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI elemento

Um controlo de caixa de texto com validação incorporada para nomes de utilizador Windows e Linux.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft-compute-usernametextbox.png)

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

## <a name="sample-output"></a>Saída de exemplo

```json
"Example name"
```

## <a name="remarks"></a>Observações

- Se `constraints.required` for definida como **verdadeira,** a caixa de texto deve ter um valor para validar com sucesso. O valor predefinido é **verdadeiro.**
- `osPlatform` deve ser especificado, e pode ser **windows** ou **Linux**.
- `constraints.regex` é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com sucesso. O valor por defeito é **nulo.**
- `constraints.validationMessage` é uma cadeia para visualizar quando o valor da caixa de texto falha na validação especificada por `constraints.regex` . Se não for especificado, as mensagens de validação incorporadas da caixa de texto são utilizadas. O valor por defeito é **nulo.**
- Este elemento tem validação incorporada que se baseia no valor especificado para `osPlatform` . A validação incorporada pode ser usada juntamente com uma expressão regular personalizada. Se for especificado um `constraints.regex` valor, então ambas as validações incorporadas e personalizadas são desencadeadas.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

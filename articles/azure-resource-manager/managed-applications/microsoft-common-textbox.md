---
title: Elemento UI textBox
description: Descreve o elemento Microsoft.Common.TextBox UI para o portal Azure. Utilize para adicionar texto não formado.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652284"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI

Um controlo que pode ser usado para editar texto não formado.

## <a name="ui-sample"></a>Amostra de UI

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

## <a name="sample-output"></a>Resultado da amostra

```json
"my text value"
```

## <a name="remarks"></a>Observações

- Se `constraints.required` for definido como **verdadeiro,** então a caixa de texto deve ter um valor para validar com sucesso. O valor predefinido é **falso**.
- `constraints.regex`é um padrão de expressão regular JavaScript. Se especificado, então o valor da caixa de texto deve corresponder ao padrão para validar com sucesso. O valor predefinido é **nulo.**
- `constraints.validationMessage`é uma corda para exibir quando o valor da caixa de texto falha na validação. Se não especificadas, as mensagens de validação incorporadas da caixa de texto são utilizadas. O valor predefinido é **nulo.**
- É possível especificar um `constraints.regex` valor `constraints.required` para quando é definido como **falso**. Neste cenário, não é necessário um valor para que a caixa de texto validar com sucesso. Se um for especificado, deve corresponder ao padrão de expressão regular.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns nos elementos UI, consulte [elementos CreateUiDefinition](create-uidefinition-elements.md).

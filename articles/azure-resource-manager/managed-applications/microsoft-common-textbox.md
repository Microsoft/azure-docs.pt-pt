---
title: Elemento UI textBox
description: Descreve o elemento UI microsoft.common.textbox para o portal Azure. Utilize para adicionar texto não testado.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652284"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI elemento

Um controlo que pode ser usado para editar texto não testado.

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

- Se `constraints.required` for definida como **verdadeira,** a caixa de texto deve ter um valor para validar com sucesso. O valor predefinido é **falso**.
- `constraints.regex`é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com sucesso. O valor por defeito é **nulo.**
- `constraints.validationMessage`é uma cadeia para exibir quando o valor da caixa de texto falha na validação. Se não for especificado, as mensagens de validação incorporadas da caixa de texto são utilizadas. O valor por defeito é **nulo.**
- É possível especificar um valor para `constraints.regex` quando é definido como `constraints.required` **falso**. Neste cenário, não é necessário um valor para que a caixa de texto valide com sucesso. Se for especificado, deve corresponder ao padrão de expressão regular.

## <a name="next-steps"></a>Próximos passos

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

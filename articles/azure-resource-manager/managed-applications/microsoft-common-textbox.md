---
title: Elemento UI textBox
description: Descreve o elemento UI microsoft.common.textbox para o portal Azure. Utilize para adicionar texto não testado.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124334"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI elemento

Um elemento de interface de utilizador (UI) que pode ser usado para adicionar texto não testado. O `Microsoft.Common.TextBox` elemento é um campo de entrada de linha única, mas suporta entrada multi-linha com a `multiLine` propriedade.

## <a name="ui-sample"></a>Amostra de UI

O `TextBox` elemento utiliza uma caixa de texto de linha única ou multi-linha.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Microsoft.Common.TextBox caixa de texto de linha única.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Microsoft.Common.TextBox caixa de texto multi-line.":::

## <a name="schema"></a>Esquema

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
"contoso123"
```

## <a name="remarks"></a>Observações

- Utilize a `toolTip` propriedade para visualizar texto sobre o elemento quando o cursor do rato estiver sobre o símbolo de informação.
- A `placeholder` propriedade é texto de ajuda que desaparece quando o utilizador começa a editar. Se os `placeholder` dois `defaultValue` são definidos, o `defaultValue` tem precedência e é mostrado.
- A `multiLine` propriedade é boolean, `true` ou `false` . Para utilizar uma caixa de texto multi-linha, desa estale a propriedade para `true` . Se não for necessária uma caixa de texto multi-linha, desa um conjunto de propriedades `false` ou exclua a propriedade. Para novas linhas, a saída JSON mostra `\n` para o feed de linha. A caixa de texto multi-linha aceita `\r` para uma devolução de transporte (CR) e `\n` para um feed de linha (LF). Por exemplo, um valor predefinido pode incluir `\r\n` especificar CRLF.
- Se `constraints.required` estiver definido para , `true` então a caixa de texto deve ter um valor para validar com sucesso. O valor predefinido é `false`.
- A `validations` propriedade é um conjunto onde você adiciona condições para verificar o valor fornecido na caixa de texto.
- A `regex` propriedade é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com sucesso. O valor predefinido é `null`. Para obter mais informações sobre a sintaxe regex, consulte [referência rápida de expressão regular](/dotnet/standard/base-types/regular-expression-language-quick-reference).
- A `isValid` propriedade contém uma expressão que avalia `true` `false` ou. Dentro da expressão, define-se a condição que determina se a caixa de texto é válida.
- A `message` propriedade é uma cadeia para exibir quando o valor da caixa de texto falha na validação.
- É possível especificar um valor para `regex` quando é definido para `required` `false` . Neste cenário, não é necessário um valor para que a caixa de texto valide com sucesso. Se for especificado, deve corresponder ao padrão de expressão regular.

## <a name="examples"></a>Exemplos

Os exemplos mostram como usar uma caixa de texto de linha única e uma caixa de texto multi-linha.

### <a name="single-line"></a>Linha única

O exemplo a seguir utiliza uma caixa de texto com o controlo [Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md) para verificar a disponibilidade de um nome de recurso.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Multi-line

Este exemplo utiliza a `multiLine` propriedade para criar uma caixa de texto multi-linha com texto reservado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

- Para uma introdução à criação de definições de UI, consulte [CreateUiDefinition.jspara a experiência de criação de aplicações geridas pela Azure.](create-uidefinition-overview.md)
- Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

---
title: Elemento UI textBox
description: Descreve o elemento UI microsoft.common.textbox para o portal Azure. Utilize para adicionar texto não testado.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474313"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI elemento

Um controlo que pode ser usado para editar texto não testado.

## <a name="ui-sample"></a>Amostra de UI

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>Esquema

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
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

- Se `constraints.required` for definida como **verdadeira,** a caixa de texto deve ter um valor para validar com sucesso. O valor predefinido é **falso**.
- A `validations` propriedade é um conjunto onde você adiciona condições para verificar o valor fornecido na caixa de texto.
- A `regex` propriedade é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com sucesso. O valor por defeito é **nulo.**
- A `isValid` propriedade contém uma expressão que avalia a verdade ou falsa. Dentro da expressão, define-se a condição que determina se a caixa de texto é válida.
- A `message` propriedade é uma cadeia para exibir quando o valor da caixa de texto falha na validação.
- É possível especificar um valor para `regex` quando é definido como `required` **falso**. Neste cenário, não é necessário um valor para que a caixa de texto valide com sucesso. Se for especificado, deve corresponder ao padrão de expressão regular.
- A `placeholder` propriedade é texto de ajuda que desaparece quando o utilizador começa a editar. Se os `placeholder` dois `defaultValue` são definidos, o `defaultValue` tem precedência e é mostrado.

## <a name="example"></a>Exemplo

O exemplo a seguir utiliza uma caixa de texto com o controlo [Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md) para verificar a disponibilidade de um nome de recurso.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à criação de definições de UI, consulte [Começar com CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de UI, consulte [os elementos CreateUiDefinition](create-uidefinition-elements.md).

---
title: Políticas de autor para propriedades de matriz em recursos
description: Aprenda a trabalhar com parâmetros de matriz e expressões de linguagem de matriz, avaliar o alias [*] e acrescentar elementos com regras de definição de Azure Policy.
ms.date: 03/06/2019
ms.topic: conceptual
ms.openlocfilehash: 96598918f0dbcc2f56e8ccc316844ee768306b75
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463513"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Políticas de autor para propriedades de matriz nos recursos do Azure

Azure Resource Manager propriedades são normalmente definidas como cadeias de caracteres e boolianos. Quando existe uma relação um-para-muitos, as propriedades complexas são definidas como matrizes. Em Azure Policy, as matrizes são usadas de várias maneiras diferentes:

- O tipo de um [parâmetro de definição](../concepts/definition-structure.md#parameters), para fornecer várias opções
- Parte de uma [regra de política](../concepts/definition-structure.md#policy-rule) usando as condições **em** ou **notIn**
- Parte de uma regra de política que avalia o [\[\*\] alias](../concepts/definition-structure.md#understanding-the--alias) para avaliar cenários específicos, como **nenhum**, **qualquer**ou **todos**
- No [efeito de acréscimo](../concepts/effects.md#append) para substituir ou adicionar a uma matriz existente

Este artigo aborda cada uso por Azure Policy e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetros

### <a name="define-a-parameter-array"></a>Definir uma matriz de parâmetros

A definição de um parâmetro como uma matriz permite a flexibilidade da política quando mais de um valor é necessário.
Essa definição de política permite qualquer local único para o parâmetro **allowedLocations** e o padrão _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Como o **tipo** era _String_, apenas um valor pode ser definido ao atribuir a política. Se essa política for atribuída, os recursos no escopo só serão permitidos em uma única região do Azure. A maioria das definições de políticas precisa permitir uma lista de opções aprovadas, como permitir _eastus2_, _eastus_e _westus2_.

Para criar a definição de política para permitir várias opções, use o **tipo**de _matriz_ . A mesma política pode ser reescrita da seguinte maneira:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Quando uma definição de política é salva, a propriedade **Type** em um parâmetro não pode ser alterada.

Essa nova definição de parâmetro usa mais de um valor durante a atribuição de política. Com a propriedade de matriz **allowedValues** definida, os valores disponíveis durante a atribuição são limitados ainda mais à lista predefinida de opções. O uso de **allowedValues** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passar valores para uma matriz de parâmetros durante a atribuição

Ao atribuir a política por meio do portal do Azure, um parâmetro do **tipo** _matriz_ é exibido como uma única caixa de texto. A dica diz "usar; para separar valores. (por exemplo, Londres; Nova York) ". Para passar os valores de local permitidos de _eastus2_, _eastus_e _westus2_ para o parâmetro, use a seguinte cadeia de caracteres:

`eastus2;eastus;westus2`

O formato do valor do parâmetro é diferente ao usar CLI do Azure, Azure PowerShell ou a API REST. Os valores são passados por meio de uma cadeia de caracteres JSON que também inclui o nome do parâmetro.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Para usar essa cadeia de caracteres com cada SDK, use os seguintes comandos:

- CLI do Azure: comando [AZ Policy atribuition Create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) com **parâmetros de parâmetro**
- Azure PowerShell: cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com o parâmetro **PolicyParameter**
- API REST: na operação _Put_ [criar](/rest/api/resources/policyassignments/create) como parte do corpo da solicitação como o valor da propriedade **Properties. Parameters**

## <a name="policy-rules-and-arrays"></a>Regras de política e matrizes

### <a name="array-conditions"></a>Condições de matriz

As [condições](../concepts/definition-structure.md#conditions) de regra de política que uma _matriz_
**tipo** de parâmetro pode ser usada com o são limitadas a `in` e `notIn`. Use a seguinte definição de política com a condição `equals` como um exemplo:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

A tentativa de criar essa definição de política por meio do portal do Azure leva a um erro como esta mensagem de erro:

- "A política ' {GUID} ' não pôde ser parametrizada devido a erros de validação. Verifique se os parâmetros da política estão definidos corretamente. O resultado da avaliação da exceção interna da expressão de linguagem ' [Parameters (' allowedLocations ')] ' é do tipo ' array ', o tipo esperado é ' String '. '. "

O **tipo** de condição esperado `equals` é _String_. Como **allowedLocations** é definido como _matriz_de tipo, o mecanismo de política avalia a expressão de idioma e gera o erro. Com a condição de `in` e `notIn`, o mecanismo de política espera a _matriz_ de **tipo** na expressão de idioma. Para resolver essa mensagem de erro, altere `equals` para `in` ou `notIn`.

### <a name="evaluating-the--alias"></a>Avaliando o alias [*]

Aliases com **[\*]** anexados ao seu nome indicam que o **tipo** é uma _matriz_. Em vez de avaliar o valor de toda a matriz, **[\*]** torna possível avaliar cada elemento da matriz. Há três cenários em que essa avaliação por item é útil em: nenhum, qualquer e tudo.

O mecanismo de política aciona o **efeito** em **seguida** somente quando a regra **If** é avaliada como true.
Esse fato é importante entender no contexto da maneira que **[\*]** avalia cada elemento individual da matriz.

A regra de política de exemplo para a tabela de cenário abaixo:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

A matriz **ipRules** é a seguinte para a tabela de cenário abaixo:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Para cada exemplo de condição abaixo, substitua `<field>` por `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Os resultados a seguir são o resultado da combinação da condição e a regra de política de exemplo e a matriz de valores existentes acima:

|Condição |Resultado |Explicação |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nada |Um elemento de matriz é avaliado como falso (127.0.0.1! = 127.0.0.1) e outro como verdadeiro (127.0.0.1! = 192.168.1.1), portanto, a condição não é **igual** a _false_ e o efeito não é disparado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito de política |Ambos os elementos da matriz são avaliados como verdadeiros (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), portanto, a condição não é **igual** a _true_ e o efeito é disparado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito de política |Um elemento de matriz é avaliado como verdadeiro (127.0.0.1 = = 127.0.0.1) e outro como falso (127.0.0.1 = = 192.168.1.1), portanto, a condição **Equals** é _false_. O operador lógico é avaliado como verdadeiro (**não** _falso_), portanto, o efeito é disparado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito de política |Ambos os elementos da matriz são avaliados como falso (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), portanto, a condição **Equals** é _false_. O operador lógico é avaliado como verdadeiro (**não** _falso_), portanto, o efeito é disparado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito de política |Um elemento de matriz é avaliado como falso (127.0.0.1! = 127.0.0.1) e outro como verdadeiro (127.0.0.1! = 192.168.1.1), portanto, a condição não é **igual** a _false_. O operador lógico é avaliado como verdadeiro (**não** _falso_), portanto, o efeito é disparado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nada |Ambos os elementos de matriz são avaliados como verdadeiros (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), portanto, a condição de não é **igual** a _true_. O operador lógico é avaliado como falso (**não** _verdadeiro_), portanto, o efeito não é disparado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nada |Um elemento de matriz é avaliado como verdadeiro (127.0.0.1 = = 127.0.0.1) e outro como falso (127.0.0.1 = = 192.168.1.1), portanto, a condição **Equals** é _false_ e o efeito não é disparado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nada |Ambos os elementos da matriz são avaliados como falso (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), portanto, a condição **Equals** é _false_ e o efeito não é disparado. |

## <a name="the-append-effect-and-arrays"></a>As matrizes e o efeito de acréscimo

O [efeito de acréscimo](../concepts/effects.md#append) comporta-se de maneira diferente dependendo de se o **Details. Field** é um alias **[\*]** ou não.

- Quando não é um alias **[\*]** , Append substitui toda a matriz pela propriedade **Value**
- Quando um alias **[\*]** , Append adiciona a propriedade **Value** à matriz existente ou cria a nova matriz

Para obter mais informações, consulte os [exemplos de acréscimo](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [corrigir recursos sem conformidade](remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
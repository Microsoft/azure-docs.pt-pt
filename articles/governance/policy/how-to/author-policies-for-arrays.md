---
title: Políticas de autor para propriedades de matriz em recursos do Azure
description: Aprenda a criar parâmetros de matriz, criar regras para a matriz de expressões de idioma, avaliar o alias [*] e a acrescentar elementos numa matriz existente com as regras de definição de política do Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 62267a4549355212a18654ff9781b2164ba19fa9
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57864043"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Políticas de autor para propriedades de matriz em recursos do Azure

Propriedades do Gestor de recursos do Azure normalmente são definidas como cadeias de caracteres e booleanos. Quando existe uma relação um-para-muitos, propriedades complexas em vez disso, são definidas como matrizes. No Azure Policy, as matrizes são utilizadas de várias maneiras diferentes:

- O tipo de um [parâmetro de definição de](../concepts/definition-structure.md#parameters), para fornecer várias opções
- Parte de um [regra de política](../concepts/definition-structure.md#policy-rule) utilizando as condições **no** ou **notIn**
- Parte de uma regra de política que avalia o [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) para avaliar os cenários específicos, como **None**, **qualquer**, ou  **Todos os**
- Na [acrescentar efeito](../concepts/effects.md#append) para substituir ou adicionar a uma matriz existente

Este artigo abrange cada utilização pela política do Azure e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetro

### <a name="define-a-parameter-array"></a>Definir uma matriz de parâmetros

Definir um parâmetro como uma matriz permite a flexibilidade de política quando mais de um valor é necessário.
Esta definição de política permite que qualquer localização única para o parâmetro **allowedLocations** e assume a predefinição _eualeste2_:

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

Como **tipo** foi _cadeia de caracteres_, apenas pode ser definido um valor quando atribuir a política. Se for atribuída esta política, os recursos no âmbito só são permitidos numa única região do Azure. Tem de permitir para obter uma lista de opções aprovadas, como permitir que a maioria das definições de políticas _eualeste2_, _eastus_, e _westus2_.

Para criar a definição de política para permitir que várias opções, utilize o _matriz_ **tipo**. A mesma política pode ser reescrita da seguinte forma:

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
> Depois de uma definição de política é guardada, o **tipo** propriedade num parâmetro não pode ser alterada.

Esta nova definição de parâmetro demora mais de um valor durante a atribuição de política. Com a propriedade de matriz **allowedValues** definido, os valores disponíveis durante a atribuição são mais limitadas para a lista predefinida de opções. Usar **allowedValues** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Transmita os valores para uma matriz de parâmetros durante a atribuição

Ao atribuir a política através do portal do Azure, um parâmetro de **tipo** _matriz_ é apresentado como uma única caixa de texto. A sugestão diz "utilizar; para separar os valores. (por exemplo, Londres; New York) ". Para passar os valores de localização permitido de _eualeste2_, _eastus_, e _westus2_ para o parâmetro, utilize a seguinte cadeia:

`eastus2;eastus;westus2`

O formato para o valor do parâmetro é diferente ao utilizar a CLI do Azure, Azure PowerShell ou a API REST. Os valores são transmitidos através de uma cadeia de caracteres do JSON que também inclui o nome do parâmetro.

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

Para usar essa cadeia de caracteres com cada SDK, utilize os seguintes comandos:

- CLI do Azure: Comando [criação da atribuição de política de az](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) com o parâmetro **params**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com o parâmetro **PolicyParameter**
- REST API: Na _colocar_ [crie](/rest/api/resources/policyassignments/create) operação como parte do corpo do pedido como o valor do **properties.parameters** propriedade

## <a name="policy-rules-and-arrays"></a>Regras de política e matrizes

### <a name="array-conditions"></a>Condições de matriz

A regra de política [condições](../concepts/definition-structure.md#conditions) que um _matriz_
**tipo** do parâmetro pode ser utilizado com está limitado a `in` e `notIn`. Realizar a seguinte definição de política com a condição `equals` como exemplo:

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

A tentar criar esta definição de política por meio das oportunidades de potenciais de portais do Azure a um erro como esta mensagem de erro:

- "A política '{GUID}' não foi possível parametrizar devido a erros de validação. Verifique se os parâmetros de política estão definidos corretamente. A exceção interna "avaliação do resultado da expressão de linguagem [parameters('allowedLocations')] é o tipo"Array", tipo esperado é 'String' '."

A esperada **tipo** da condição `equals` é _cadeia_. Uma vez que **allowedLocations** é definido como **tipo** _matriz_, o motor de política avalia a expressão de linguagem e emite o erro. Com o `in` e `notIn` condição, o mecanismo da diretiva espera que o **tipo** _matriz_ na expressão de linguagem. Para resolver esta mensagem de erro, altere `equals` a um `in` ou `notIn`.

### <a name="evaluating-the--alias"></a>Avaliar o alias [*]

Aliases que tenham **[\*]** anexado ao respetivo nome indicar o **tipo** é um _matriz_. Em vez de avaliar o valor da matriz inteira, **[\*]** torna possível avaliar a cada elemento da matriz. Existem três cenários por item avaliação é útil para: Nenhum, qualquer um e tudo.

Os acionadores de motor de política do **efeito** na **, em seguida,** apenas quando o **se** regra avalia como verdadeiro.
Esse fato é importante compreender no contexto do modo como a **[\*]** avalia cada elemento individual da matriz.

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

O **ipRules** matriz é o seguinte para a tabela de cenário abaixo:

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

Para cada exemplo de condição abaixo, substitua `<field>` com `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Os resultados seguintes são o resultado da combinação da condição e a regra de política de exemplo e a matriz de valores existentes acima:

|Condição |Resultado |Explicação |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nada |Um elemento de matriz é avaliada como false (127.0.0.1! = 127.0.0.1) e outro como true (127.0.0.1! = 192.168.1.1), pelo que a **notEquals** condição for _falso_ e o efeito não é acionado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito de política |Ambos os elementos de matriz avaliar como VERDADEIRO (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), pelo que a **notEquals** condição for _verdadeiro_ e o efeito é acionado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito de política |Um elemento de matriz é avaliada como true (127.0.0.1 = = 127.0.0.1) e outro como false (127.0.0.1 = = 192.168.1.1), pelo que a **é igual a** condição for _falso_. O operador lógico é avaliada como true (**não** _falso_), por isso, o efeito é acionado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito de política |Ambos os elementos de matriz avaliar como false (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), pelo que a **é igual a** condição for _falso_. O operador lógico é avaliada como true (**não** _falso_), por isso, o efeito é acionado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito de política |Um elemento de matriz é avaliada como false (127.0.0.1! = 127.0.0.1) e outro como true (127.0.0.1! = 192.168.1.1), pelo que a **notEquals** condição for _falso_. O operador lógico é avaliada como true (**não** _falso_), por isso, o efeito é acionado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nada |Ambos os elementos de matriz avaliar como VERDADEIRO (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), pelo que a **notEquals** condição for _verdadeiro_. O operador lógico é avaliada como false (**não** _verdadeiro_), por isso, o efeito não é acionado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nada |Um elemento de matriz é avaliada como true (127.0.0.1 = = 127.0.0.1) e outro como false (127.0.0.1 = = 192.168.1.1), pelo que a **é igual a** condição for _falso_ e o efeito não é acionado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nada |Ambos os elementos de matriz avaliar como false (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), pelo que a **é igual a** condição for _falso_ e o efeito não é acionado. |

## <a name="the-append-effect-and-arrays"></a>O efeito de acréscimo e matrizes

O [acrescentar efeito](../concepts/effects.md#append) tem um comportamento diferente dependendo se o **details.field** é um **[\*]** alias ou não.

- Quando não uma **[\*]** alias, acrescentar substitui a matriz inteira com o **valor** propriedade
- Quando um **[\*]** alias, Acrescentar adiciona a **valor** propriedade para o existente de matriz ou cria a nova matriz

Para obter mais informações, consulte a [acrescentar exemplos](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Passos Seguintes

- Reveja exemplos em [exemplos do Azure Policy](../samples/index.md)
- Reveja o [estrutura de definição de política](../concepts/definition-structure.md)
- Revisão [Noções básicas sobre os efeitos de política](../concepts/effects.md)
- Compreender como [criar políticas programaticamente](programmatically-create.md)
- Saiba como [remediar recursos não compatíveis](remediate-resources.md)
- Rever o que é um grupo de gestão, com [Organizar os recursos com grupos de gestão do Azure](../../management-groups/overview.md)
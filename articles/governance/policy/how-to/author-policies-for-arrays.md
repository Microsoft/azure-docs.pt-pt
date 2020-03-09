---
title: Políticas de autor para propriedades de matriz em recursos
description: Aprenda a trabalhar com parâmetros de matriz e expressões linguísticas matrizes, avalie o pseudónimo [*] e aprenda elementos com regras de definição de Política Azure.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360211"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Políticas de autor para propriedades de matriz sobre recursos Azure

As propriedades do Gestor de Recursos Azure são geralmente definidas como cordas e booleans. Quando existe uma relação de um a muitos, propriedades complexas são definidas como matrizes. Na Política Azure, as matrizes são usadas de várias maneiras diferentes:

- O tipo de parâmetro de [definição,](../concepts/definition-structure.md#parameters)para fornecer múltiplas opções
- Parte de uma [regra política](../concepts/definition-structure.md#policy-rule) usando as condições **dentro** ou **nãoIn**
- Parte de uma regra política que avalia o [\[\*\] pseudónimo](../concepts/definition-structure.md#understanding-the--alias) para avaliar:
  - Cenários como **Nenhum,** **Nenhum,** ou **Todos**
  - Cenários complexos com **contagem**
- No [efeito apêndice](../concepts/effects.md#append) para substituir ou adicionar a uma matriz existente

Este artigo abrange cada utilização da Política Azure e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetros

### <a name="define-a-parameter-array"></a>Defina uma matriz de parâmetros

Definir um parâmetro como matriz permite a flexibilidade da política quando é necessário mais do que um valor.
Esta definição de política permite qualquer localização única para o parâmetro **permitidoLocalizações** e incumprimentos para _lesteus2_:

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

Como **o tipo** era _corda_, apenas um valor pode ser definido ao atribuir a apólice. Se esta política for atribuída, os recursos de âmbito só são permitidos numa única região do Azure. A maioria das definições de políticas devem permitir uma lista de opções aprovadas, tais como permitir _o leste-_ _eastus_, e _westus2_.

Para criar a definição de política para permitir múltiplas opções, utilize o **tipo**de _matriz_ . A mesma política pode ser reescrita da seguinte forma:

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
> Uma vez que uma definição de política é guardada, a propriedade **tipo** em um parâmetro não pode ser alterada.

Esta nova definição de parâmetro supõe mais do que um valor durante a atribuição de políticas. Com a propriedade de matriz **permitidaValores** definidos, os valores disponíveis durante a atribuição estão ainda limitados à lista de escolhas predefinidas. A utilização de **Valores permitidos** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passe valores para uma matriz de parâmetros durante a atribuição

Ao atribuir a apólice através do portal Azure, um parâmetro de _matriz_ **de tipo** é apresentado como uma única caixa de texto. A dica diz "Use; para separar valores. (por exemplo, Londres; Nova Iorque". Para passar os valores de localização permitidos de _eastus2_, _eastus_, e _westus2_ para o parâmetro, use a seguinte corda:

`eastus2;eastus;westus2`

O formato para o valor do parâmetro é diferente quando se utiliza O Azure CLI, o Azure PowerShell ou o REST API. Os valores são passados através de uma corda JSON que também inclui o nome do parâmetro.

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

Para utilizar esta cadeia com cada SDK, utilize os seguintes comandos:

- Azure CLI: Atribuição de políticas do Comando [az cria](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) com parâmetros de **parâmetros**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com parâmetro de **política** de parâmetros
- REST API: No _PUT_ [criar](/rest/api/resources/policyassignments/create) operação como parte do Órgão de Pedido como o valor da **propriedade properties.parâmetros**

## <a name="policy-rules-and-arrays"></a>Regras e matrizes políticas

### <a name="array-conditions"></a>Condições de matriz

As [condições](../concepts/definition-structure.md#conditions) de regra da política com as que um _conjunto_
**tipo** de parâmetro podem ser utilizados limitam-se a `in` e `notIn`. Tome a seguinte definição de política com condição `equals` como exemplo:

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

Tentar criar esta definição de política através do portal Azure leva a um erro como esta mensagem de erro:

- "A política '{GUID}' não podia ser parametrizada devido a erros de validação. Por favor, verifique se os parâmetros da política estão devidamente definidos. A exceção interna 'Resultado de avaliação da expressão linguística '['parâmetros')' é o tipo 'Array', o tipo esperado é 'String'.".

O **tipo** de condição esperado `equals` é _corda_. Uma vez **que as localizações permitidas** são definidas como _matriz_de **tipo,** o motor de política avalia a expressão linguística e lança o erro. Com a condição `in` e `notIn`, o motor de política espera a _matriz_ **tipo** na expressão linguística. Para resolver esta mensagem de erro, mude `equals` para `in` ou `notIn`.

### <a name="evaluating-the--alias"></a>Avaliação do pseudónimo [*]

Os aliases que **\[\*\]** ligados ao seu nome indicam que o **tipo** é uma _matriz_. Em vez de avaliar o valor de toda a matriz, **\[\*\]** permite avaliar cada elemento da matriz individualmente, com lógica e entre eles. Existem três cenários padrão que esta avaliação por item é útil em: _Nenhum, qualquer_, _qualquer_ou _todos os_ elementos correspondem. Para cenários complexos, [a contagem de](../concepts/definition-structure.md#count)utilização.

O motor de política desencadeia o **efeito** só nessa **altura** quando a regra **se** avalia como verdadeira.
Este facto é importante de compreender no contexto da forma como **\[\*\]** avalia cada elemento individual da matriz.

A regra da política de exemplo para o quadro de cenário seguem:

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

O array **ipRules** é o seguinte para o quadro de cenário abaixo:

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

Para cada condição exemplo abaixo, substitua `<field>` por `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Os seguintes resultados são o resultado da combinação da condição e da regra política de exemplo e de um conjunto de valores existentes acima:

|Condição |Resultado | Cenário |Explicação |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nada, nada. |Nenhum jogo |Um elemento matriz avalia como falso (127.0.0.1 != 127.0.0.1) e um como verdadeiro (127.0.0.1 != 192.168.1.1), pelo que a condição **de notEquals** é _falsa_ e o efeito não é desencadeado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito político |Nenhum jogo |Ambos os elementos da matriz avaliam como verdadeiro (10.0.4.1 != 127,0.0.1 e 10.0.4.1 != 192.168.1.1), pelo que a condição **notEquals** é _verdadeira_ e o efeito é desencadeado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito político |Um ou mais fósforos |Um elemento matriz avalia como falso (127.0.0.1 != 127.0.0.1) e um como verdadeiro (127.0.0.1 != 192.168.1.1), pelo que a condição **de notEquals** é _falsa_. O operador lógico avalia como verdadeiro (**não** _falso_), pelo que o efeito é desencadeado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nada, nada. |Um ou mais fósforos |Ambos os elementos da matriz avaliam como verdadeiro (10.0.4.1 != 127,0.0.1 e 10.0.4.1 != 192.168.1.1), pelo que a condição **não igual** é _verdadeira._ O operador lógico avalia como falso (**não** _verdadeiro_), pelo que o efeito não é desencadeado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito político |Nem todos combinam. |Um elemento matriz avalia como verdadeiro (127.0.0.1 == 127,0.0.1) e um como falso (127,0.0.1 == 192.168.1.1), pelo que a condição **de Equals** é _falsa_. O operador lógico avalia como verdadeiro (**não** _falso_), pelo que o efeito é desencadeado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito político |Nem todos combinam. |Ambos os elementos da matriz avaliam como falso (10.0.4.1 == 127,0.0.1 e 10,0.4.1 == 192.168.1.1), pelo que a condição **de Equals** é _falsa_. O operador lógico avalia como verdadeiro (**não** _falso_), pelo que o efeito é desencadeado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nada, nada. |Todos os fósforos |Um elemento matriz avalia como verdadeiro (127.0.0.1 == 127,0.0.1) e um como falso (127,0.0.1 == 192.168.1.1), pelo que a condição **de Equals** é _falsa_ e o efeito não é desencadeado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nada, nada. |Todos os fósforos |Ambos os elementos da matriz avaliam como falso (10.0.4.1 == 127,0.0.1 e 10.0.4.1 == 192.168.1.1), pelo que a condição **de Equals** é _falsa_ e o efeito não é desencadeado. |

## <a name="the-append-effect-and-arrays"></a>O efeito apêndice e as matrizes

O [efeito apêndice](../concepts/effects.md#append) comporta-se de forma diferente dependendo se os **detalhes.field** for um\[\* **\]** pseudónimo ou não.

- Quando não é um **pseudónimo\[\*\],** o apêndice substitui toda a matriz pela propriedade de **valor**
- Quando um **\[\*** \]pseudónimo, o apêndice adiciona a propriedade de **valor** à matriz existente ou cria a nova matriz

Para mais informações, consulte os [exemplos do apêndice.](../concepts/effects.md#append-examples)

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Aprenda a [remediar recursos não conformes](remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)

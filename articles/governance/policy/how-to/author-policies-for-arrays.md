---
title: Políticas de autor para propriedades de matrizes em recursos
description: Aprenda a trabalhar com parâmetros de matriz e expressões linguísticas de matriz, avalie o pseudónimo [*] e apedguia elementos com regras de definição de Política de Azure.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: c67982197c0161d99f29747d6fd11166cba86079
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576902"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Políticas de autor para propriedades de matrizes em recursos Azure

As propriedades do Gestor de Recursos Azure são geralmente definidas como cordas e booleans. Quando existe uma relação de um para muitos, propriedades complexas são definidas como matrizes. Na Política Azure, as matrizes são usadas de várias maneiras:

- O tipo de parâmetro de [definição,](../concepts/definition-structure.md#parameters)para fornecer múltiplas opções
- Parte de uma [regra de política](../concepts/definition-structure.md#policy-rule) usando as condições **em** ou **não**
- Parte de uma regra política que avalia o [ \[ \* \] pseudónimo](../concepts/definition-structure.md#understanding-the--alias) para avaliar:
  - Cenários como **Nenhum**, **Qualquer**, ou **Todos**
  - Cenários complexos com **contagem**
- No [efeito apêndice](../concepts/effects.md#append) para substituir ou adicionar a uma matriz existente

Este artigo cobre cada utilização pela Azure Policy e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetros

### <a name="define-a-parameter-array"></a>Definir uma matriz de parâmetros

Definir um parâmetro como um array permite a flexibilidade da política quando é necessário mais do que um valor.
Esta definição de política permite qualquer localização para o parâmetro **permitidoLocações** e predefinições a _leste 2_:

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

Como **o tipo** era _string,_ apenas um valor pode ser definido ao atribuir a apólice. Se esta política for atribuída, os recursos de âmbito só são permitidos numa única região do Azure. A maioria das definições de políticas tem de permitir uma lista de opções aprovadas, tais como permitir _que o Eastus2,_ _o Eastus_e _o Westus2._

Para criar a definição de política para permitir várias opções, utilize o **tipo** _de matriz_ . A mesma política pode ser reescrita da seguinte forma:

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

Esta nova definição de parâmetros requer mais do que um valor durante a atribuição de políticas. Com a propriedade array **permitida Valores definidos,** os valores disponíveis durante a atribuição estão ainda limitados à lista de escolhas predefinida. O uso de **Valores Permitidos** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passe valores para um conjunto de parâmetros durante a atribuição

Ao atribuir a política através do portal Azure, é apresentado um parâmetro **de** _tipo de matriz_ como uma única caixa de texto. A dica diz "Use; para valores separados. (por exemplo, Londres; Nova Iorque)"," Para passar os valores de localização permitidos de _Eastus2,_ _Eastus_, e _Westus2_ para o parâmetro, use a seguinte cadeia:

`eastus2;eastus;westus2`

O formato para o valor do parâmetro é diferente quando se utiliza Azure CLI, Azure PowerShell ou a API REST. Os valores são passados através de uma cadeia JSON que também inclui o nome do parâmetro.

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

- Azure CLI: Atribuição de política de comando [az criar](/cli/azure/policy/assignment#az-policy-assignment-create) com **parâmetros params**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com parâmetro **PolicyParameter**
- REST API: No _PUT_ [criar](/rest/api/resources/policyassignments/create) operação como parte do Corpo de Pedido como valor do **imóvel.parâmetros** propriedade

## <a name="policy-rules-and-arrays"></a>Regras e matrizes políticas

### <a name="array-conditions"></a>Condições de matriz

As [condições](../concepts/definition-structure.md#conditions) de regra de política com as quais um tipo de parâmetro de _matriz_ 
 **type** pode ser utilizado limitam-se a `in` e `notIn` . Tome como exemplo a seguinte definição de política com `equals` condição:

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

- "A política '{GUID}' não pôde ser parametrizada devido a erros de validação. Verifique se os parâmetros da política estão corretamente definidos. A exceção interior "Resultado da avaliação da expressão linguística '[parâmetros('permitidos')]" é o tipo 'Matriz', tipo esperado é 'String'.'."

O **tipo** de condição esperada `equals` é a _corda._ Uma **vez que as indicações permitidas** são definidas como tipo de **type** _matriz,_ o motor de política avalia a expressão linguística e lança o erro. Com a `in` condição e `notIn` condição, o motor de política espera o **tipo** _de matriz_ na expressão linguística. Para resolver esta mensagem de erro, mude `equals` para qualquer um ou `in` `notIn` .

### <a name="evaluating-the--alias"></a>Avaliação do pseudónimo [*]

Pseudónimos ligados **\[\*\]** ao seu nome indicam que o **tipo** é uma _matriz_. Em vez de avaliar o valor de toda a matriz, **\[\*\]** permite avaliar cada elemento da matriz individualmente, com lógica e entre eles. Existem três cenários padrão em que a avaliação por item é útil em: _Nenhum_, _Qualquer_, ou _Todos os_ elementos correspondem. Para cenários complexos, use [a contagem](../concepts/definition-structure.md#count).

O motor de política só ativa o **efeito** **quando** a regra **se** avalia como verdadeira.
Este facto é importante para entender no contexto da forma como **\[\*\]** avalia cada elemento individual da matriz.

A regra da política de exemplo para o quadro de cenários abaixo:

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

A matriz **ipRules** é a seguinte para a tabela de cenários abaixo:

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

Para cada exemplo de condição abaixo, `<field>` substitua por `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"` .

Os seguintes resultados são o resultado da combinação da condição e da regra da política de exemplo e da matriz dos valores existentes acima:

|Condição |Resultado | Cenário |Explicação |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nenhumas |Nenhum combina |Um elemento de matriz avalia como falso (127.0.0.1 != 127.0.0.1) e um como verdadeiro (127.0.0.1 != 192.168.1.1), pelo que a condição **notEquals** é _falsa_ e o efeito não é acionado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito político |Nenhum combina |Ambos os elementos de matriz avaliam como verdadeiros (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1,1), pelo que a condição **notEquals** é _verdadeira_ e o efeito é desencadeado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito político |Um ou mais fósforo |Um elemento de matriz avalia como falso (127.0.0.1 != 127.0.0.1) e um como verdadeiro (127.0.0.1 != 192.168.1.1), pelo que a condição **notEquals** é _falsa_. O operador lógico avalia como verdadeiro **(não** _falso),_ pelo que o efeito é desencadeado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nenhumas |Um ou mais fósforo |Ambos os elementos de matriz avaliam como verdadeiros (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1,1), pelo que a condição **notEquals** é _verdadeira_. O operador lógico avalia como falso **(não** _é verdade),_ para que o efeito não seja desencadeado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito político |Nem todos combinam. |Um elemento de matriz avalia como verdadeiro (127.0.0.1 == 127.0.0.1) e um como falso (127.0.0.0.1 == 192.168.1.1), pelo que a condição **de Iguais** é _falsa_. O operador lógico avalia como verdadeiro **(não** _falso),_ pelo que o efeito é desencadeado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito político |Nem todos combinam. |Ambos os elementos de matriz avaliam como falso (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), pelo que a condição **de Iguais** é _falsa._ O operador lógico avalia como verdadeiro **(não** _falso),_ pelo que o efeito é desencadeado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nenhumas |Todos os fósforos |Um elemento de matriz avalia como verdadeiro (127.0.0.1 == 127.0.0.1) e um como falso (127.0.0.1 == 192.168.1.1), pelo que a condição **equal é** _falsa_ e o efeito não é desencadeado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nenhumas |Todos os fósforos |Ambos os elementos de matriz avaliam como falso (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1, pelo que a condição **de Iguais** é _falsa_ e o efeito não é acionado. |

## <a name="modifying-arrays"></a>Modificação de matrizes

O [apêndice](../concepts/effects.md#append) e [modifica propriedades](../concepts/effects.md#modify) alterando um recurso durante a criação ou atualização. Ao trabalhar com propriedades de matriz, o comportamento destes efeitos depende se a operação está a tentar modificar o  **\[\*\]** pseudónimo ou não:

> [!NOTE]
> A utilização do `modify` efeito com pseudónimos encontra-se atualmente em **pré-visualização**.

|Alias |Efeito | Resultado |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | A Política Azure anexa toda a matriz especificada nos detalhes do efeito, se faltar. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` com `add` operação | A Política Azure anexa toda a matriz especificada nos detalhes do efeito, se faltar. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` com `addOrReplace` operação | A Política Azure anexa toda a matriz especificada nos detalhes do efeito se faltar ou substituir a matriz existente. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | A política Azure anexa o membro da matriz especificado nos detalhes do efeito. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` com `add` operação | A política Azure anexa o membro da matriz especificado nos detalhes do efeito. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` com `addOrReplace` operação | A Política Azure remove todos os membros da matriz existentes e anexa o membro da matriz especificado nos detalhes do efeito. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | A Política Azure anexa um valor à `action` propriedade de cada membro da matriz. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` com `add` operação | A Política Azure anexa um valor à `action` propriedade de cada membro da matriz. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` com `addOrReplace` operação | A Azure Policy apende ou substitui a propriedade existente `action` de cada membro da matriz. |

Para mais informações, consulte os exemplos do [apêndice.](../concepts/effects.md#append-examples)

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [remediar recursos não conformes.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)

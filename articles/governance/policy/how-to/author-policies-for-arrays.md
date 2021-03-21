---
title: Políticas de autor para propriedades de matrizes em recursos
description: Aprenda a trabalhar com parâmetros de matriz e expressões linguísticas de matriz, avalie o pseudónimo [*] e apedguia elementos com regras de definição de Política de Azure.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 650b2ec6bc1bbd12cd10abb1917ef5ea2d6029e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220750"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Políticas de autor para propriedades de matrizes em recursos Azure

As propriedades do Gestor de Recursos Azure são geralmente definidas como cordas e booleans. Quando existe uma relação de um para muitos, propriedades complexas são definidas como matrizes. Na Política Azure, as matrizes são usadas de várias maneiras:

- O tipo de parâmetro de [definição,](../concepts/definition-structure.md#parameters)para fornecer múltiplas opções
- Parte de uma [regra de política](../concepts/definition-structure.md#policy-rule) usando as condições **em** ou **não**
- Parte de uma regra política que conta quantos membros da matriz satisfazem uma condição
- No [apêndice](../concepts/effects.md#append) e [modificar](../concepts/effects.md#modify) os efeitos para atualizar uma matriz existente

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

Como **o tipo** era _string,_ apenas um valor pode ser definido ao atribuir a apólice. Se esta política for atribuída, os recursos de âmbito só são permitidos numa única região do Azure. A maioria das definições de políticas tem de permitir uma lista de opções aprovadas, tais como permitir _que o Eastus2,_ _o Eastus_ e _o Westus2._

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

- Azure CLI: Atribuição de política de comando [az criar](/cli/azure/policy/assignment#az_policy_assignment_create) com **parâmetros params**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com parâmetro **PolicyParameter**
- REST API: No _PUT_ [criar](/rest/api/resources/policyassignments/create) operação como parte do Corpo de Pedido como valor do **imóvel.parâmetros** propriedade

## <a name="using-arrays-in-conditions"></a>Usando matrizes em condições

### <a name="in-and-notin"></a>`In` e `notIn`

As `in` `notIn` condições funcionam apenas com valores de matriz. Verificam a existência de um valor numa matriz. A matriz pode ser uma matriz JSON literal ou uma referência a um parâmetro de matriz. Por exemplo:

```json
{
      "field": "tags.environment",
      "in": [ "dev", "test" ]
}
```

```json
{
      "field": "location",
      "notIn": "[parameters('allowedLocations')]"
}
```

### <a name="value-count"></a>Contagem de valor

A [expressão de contagem de valor](../concepts/definition-structure.md#value-count) conta quantos membros da matriz cumprem uma condição. Fornece uma forma de avaliar a mesma condição várias vezes, usando valores diferentes em cada iteração. Por exemplo, a seguinte condição verifica se o nome do recurso corresponde a qualquer padrão de uma série de padrões:

```json
{
    "count": {
        "value": [ "test*", "dev*", "prod*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Para avaliar a expressão, a Política Azure avalia a `where` condição 3 vezes, uma vez para cada membro de `[ "test*", "dev*", "prod*" ]` , contando quantas vezes foi avaliada para `true` . Em cada iteração, o valor do membro da matriz atual é emparelhado com o `pattern` nome de índice definido por `count.name` . Este valor pode então ser referenciado dentro da `where` condição, chamando uma função especial do modelo: `current('pattern')` .

| Iteração | `current('pattern')` valor devolvido |
|:---|:---|
| 1 | `"test*"` |
| 2 | `"dev*"` |
| 3 | `"prod*"` |

A condição só é verdadeira se a contagem resultante for superior a 0.

Para tornar a condição acima mais genérica, utilize referência de parâmetro em vez de uma matriz literal:

 ```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

Quando a expressão **da contagem de valor** não está sob qualquer outra expressão de **contagem,** `count.name` é opcional e a `current()` função pode ser usada sem quaisquer argumentos:

```json
{
    "count": {
        "value": "[parameters('patterns')]",
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

**A contagem de valor** também suporta matrizes de objetos complexos, permitindo condições mais complexas. Por exemplo, a seguinte condição define um valor de etiqueta desejado para cada padrão de nome e verifica se o nome do recurso corresponde ao padrão, mas não tem o valor de etiqueta exigido:

```json
{
    "count": {
        "value": [
            { "pattern": "test*", "envTag": "dev" },
            { "pattern": "dev*", "envTag": "dev" },
            { "pattern": "prod*", "envTag": "prod" },
        ],
        "name": "namePatternRequiredTag",
        "where": {
            "allOf": [
                {
                    "field": "name",
                    "like": "[current('namePatternRequiredTag').pattern]"
                },
                {
                    "field": "tags.env",
                    "notEquals": "[current('namePatternRequiredTag').envTag]"
                }
            ]
        }
    },
    "greater": 0
}
```

Para exemplos úteis, consulte [exemplos de contagem de valor.](../concepts/definition-structure.md#value-count-examples)

## <a name="referencing-array-resource-properties"></a>Referenciação de propriedades de recursos de matriz

Muitos casos de uso requerem trabalhar com propriedades de matriz no recurso avaliado. Alguns cenários requerem referência a toda uma matriz (por exemplo, verificar o seu comprimento). Outros exigem a aplicação de uma condição a cada membro da matriz individual (por exemplo, certifique-se de que todas as regras de firewall bloqueiam o acesso a partir da internet). Compreender as diferentes formas de Azure Policy pode referenciar propriedades de recursos, e como estas referências se comportam quando se referem a propriedades de matriz é a chave para escrever condições que cobrem estes cenários.

### <a name="referencing-resource-properties"></a>Referenciação de propriedades de recursos

Propriedades de recursos podem ser referenciadas pela Azure Policy usando pseudónimos Existem duas formas de referenciar os [valores](../concepts/definition-structure.md#aliases) de uma propriedade de recursos dentro da Política Azure:

- Utilize a condição [de campo](../concepts/definition-structure.md#fields) para verificar se **todas as** propriedades de recursos selecionadas cumprem uma condição. Exemplo:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Utilizar `field()` a função para aceder ao valor de um imóvel. Exemplo:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

A condição de campo tem um comportamento implícito de "todos". Se o pseudónimo representa uma coleção de valores, verifica se todos os valores individuais cumprem a condição. A `field()` função devolve os valores representados pelo pseudónimo como-é, que podem ser manipulados por outras funções do modelo.

### <a name="referencing-array-fields"></a>Referenciação de campos de matriz

As propriedades dos recursos array são geralmente representadas por dois tipos diferentes de pseudónimos. Um pseudónimo 'normal' e [pseudónimos](../concepts/definition-structure.md#understanding-the--alias) de matriz que `[*]` lhe estão ligados:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Referenciando a matriz

O primeiro pseudónimo representa um único valor, o valor dos `stringArray` imóveis a partir do conteúdo do pedido. Como o valor dessa propriedade é uma matriz, não é muito útil em condições políticas. Por exemplo:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Esta condição compara toda a `stringArray` matriz a um único valor de corda. A maioria das condições, `equals` incluindo, apenas aceitam valores de cordas, por isso não há muito uso na comparação de uma matriz a uma corda. O cenário principal em que a referência à propriedade da matriz é útil é ao verificar se existe:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Com a `field()` função, o valor devolvido é o conjunto do conteúdo do pedido, que pode ser usado com qualquer uma das [funções](../concepts/definition-structure.md#policy-functions) de modelo suportado que aceitam argumentos de matriz. Por exemplo, a seguinte condição verifica se o comprimento de comprimento é superior a `stringArray` 0:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Referenciando a coleção de membros da matriz

Os pseudónimos que utilizam a `[*]` sintaxe representam uma **coleção de valores de propriedade selecionados a partir de uma propriedade de matriz,** que é diferente de selecionar a própria propriedade da matriz. No caso `Microsoft.Test/resourceType/stringArray[*]` de, devolve uma coleção que tem todos os membros de `stringArray` . Como mencionado anteriormente, uma `field` condição verifica que todas as propriedades de recursos selecionados cumprem a condição, pelo que a seguinte condição só é verdadeira se todos **os** membros de serem iguais `stringArray` a "valor".

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Se a matriz contiver `[*]` objetos, um pseudónimo pode ser usado para selecionar o valor de uma propriedade específica de cada membro da matriz. Exemplo:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Esta condição é verdadeira se os valores de todas as `property` propriedades são iguais a `objectArray` `"value"` . Para mais exemplos, consulte [ \[ \* \] exemplos adicionais de pseudónimos.](#appendix--additional--alias-examples)

Ao utilizar a `field()` função para referenciar um pseudónimo de matriz, o valor devolvido é um conjunto de todos os valores selecionados. Este comportamento significa que o caso de uso comum da `field()` função, a capacidade de aplicar funções de modelo aos valores de propriedade de recursos, é muito limitado. As únicas funções do modelo que podem ser usadas neste caso são as que aceitam argumentos de matriz. Por exemplo, é possível obter o comprimento da matriz com `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` . No entanto, cenários mais complexos como aplicar a função do modelo a cada membro da matriz e compará-lo com um valor desejado só são possíveis quando se utiliza a `count` expressão. Para obter mais informações, consulte [a expressão contagem de campo.](#field-count-expressions)

Para resumir, consulte o seguinte exemplo de conteúdo de recursos e os valores selecionados devolvidos por vários pseudónimos:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Ao utilizar a condição de campo no conteúdo do recurso de exemplo, os resultados são os seguintes:

| Alias | Valores selecionados |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Uma coleção vazia de valores. |
| `Microsoft.Test/resourceType/missingArray[*].property` | Uma coleção vazia de valores. |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Ao utilizar a `field()` função no conteúdo de recursos de exemplo, os resultados são os seguintes:

| Expression | Valor Devolvido |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

### <a name="field-count-expressions"></a>Expressões de contagem de campo

[As expressões de contagem](../concepts/definition-structure.md#field-count) de campo contam quantos membros da matriz cumprem uma condição e comparam a contagem com um valor-alvo. `Count` é mais intuitivo e versátil para avaliar matrizes em comparação com `field` as condições. A sintaxe é:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Quando usado sem `where` uma condição, `count` basta retornar o comprimento de uma matriz. Com o conteúdo de recursos exemplo da secção anterior, a expressão a seguir `count` é avaliada `true` para, uma vez que tem três `stringArray` membros:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Este comportamento também funciona com matrizes aninhadas. Por exemplo, a expressão a seguir `count` é avaliada, uma `true` vez que existem quatro membros de matrizes nas `nestedArray` matrizes:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

O poder `count` está na `where` condição. Quando é especificado, a Política Azure enumera os membros da matriz e avalia cada um deles contra a condição, contando quantos membros da matriz avaliados para `true` . Especificamente, em cada iteração da avaliação da condição, a `where` Azure Policy seleciona um único membro da matriz ***i** _ e avalia o conteúdo do recurso contra a `where` condição _*como se **_i_*_ é o único membro da array_*. Ter apenas um membro da matriz disponível em cada iteração fornece uma maneira de aplicar condições complexas em cada membro da matriz individual.

Exemplo:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
Para avaliar a expressão, a `count` Política Azure avalia a `where` condição 3 vezes, uma vez para cada membro de `stringArray` , contando quantas vezes foi avaliada para `true` . Quando a `where` condição se refere aos membros da `Microsoft.Test/resourceType/stringArray[*]` matriz, em vez de selecionar todos os membros `stringArray` de, ele apenas selecionará um único membro de matriz de cada vez:

| Iteração | `Microsoft.Test/resourceType/stringArray[*]`Valores selecionados | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

E assim o `count` testamento vai `1` voltar.

Aqui está uma expressão mais complexa:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iteração | Valores selecionados | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

E assim os `count` `1` retornos.

O facto de a `where` expressão ser avaliada em função de **todo** o conteúdo do pedido (com alterações apenas ao membro da matriz que está atualmente a ser enumerado) significa que a condição também `where` pode referir-se a campos fora da matriz:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| Iteração | Valores selecionados | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Expressões de contagem aninhada também são permitidas:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| Iteração de loop exterior | Valores selecionados | Iteração de loop interior | Valores selecionados |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

#### <a name="accessing-current-array-member-with-template-functions"></a>Aceder ao membro atual da matriz com funções de modelo

Ao utilizar as funções do modelo, utilize a `current()` função para aceder ao valor do membro da matriz atual ou aos valores de qualquer uma das suas propriedades. Para aceder ao valor do membro da matriz atual, passe o pseudónimo definido em ou em `count.field` qualquer um dos seus pseudónimos infantis como argumento para a `current()` função. Por exemplo:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
        "value": "[current('Microsoft.Test/resourceType/objectArray[*].property')]",
        "like": "value*"
    }
  },
  "equals": 2
}

```

| Iteração | `current()` valor devolvido | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | O valor `property` do primeiro membro `objectArray[*]` de: `value1` | `true` |
| 2 | O valor `property` do primeiro membro `objectArray[*]` de: `value2` | `true` |

#### <a name="the-field-function-inside-where-conditions"></a>A função de campo dentro onde as condições

A `field()` função também pode ser usada para aceder ao valor do membro da matriz atual, desde que a expressão **da contagem** não esteja dentro de uma condição de **existência** `field()` (função consulte sempre o recurso avaliado na condição **de se** a condição).
O comportamento de `field()` quando se refere à matriz avaliada baseia-se nos seguintes conceitos:
1. Os pseudónimos de matriz são resolvidos numa coleção de valores selecionados de todos os membros da matriz.
1. `field()` funções referentes aliases de matriz devolvem uma matriz com os valores selecionados.
1. Referindo-se ao pseudónimo de matriz contado dentro da `where` condição, devolve uma coleção com um único valor selecionado do membro da matriz que é avaliado na iteração atual.

Este comportamento significa que, quando se refere ao membro da matriz contada com uma `field()` função dentro da `where` condição, **devolve uma matriz com um único membro**. Embora isto possa não ser intuitivo, é consistente com a ideia de que os pseudónimos de matriz devolvem sempre uma coleção de propriedades selecionadas. Eis um exemplo:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iteração | Valores de expressão | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Portanto, quando há necessidade de aceder ao valor do pseudónimo de matriz contada com uma `field()` função, a maneira de fazê-lo é embrulhá-lo com uma `first()` função de modelo:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iteração | Valores de expressão | `where` Resultado da avaliação |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Para exemplos úteis, consulte [exemplos de contagem de campos.](../concepts/definition-structure.md#field-count-examples)

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

## <a name="appendix--additional--alias-examples"></a>Apêndice- exemplos adicionais de pseudónimos

Recomenda-se a utilização das [expressões](#field-count-expressions) de contagem de campo para verificar se "todos" ou "qualquer um dos" membros de uma matriz no conteúdo do pedido cumprem uma condição. No entanto, para algumas condições simples, é possível obter o mesmo resultado utilizando um acessório de campo com um pseudónimo de matriz (conforme descrito em [Referência à coleção de membros da matriz).](#referencing-the-array-members-collection) Isto pode ser útil em regras políticas que excedam o limite de expressões de **contagem** permitidas. Aqui estão exemplos para casos de uso comum:

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

|Condição |Resultado | Scenario |Explicação |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nenhumas |Nenhum combina |Um elemento de matriz avalia como falso (127.0.0.1 != 127.0.0.1) e um como verdadeiro (127.0.0.1 != 192.168.1.1), pelo que a condição **notEquals** é _falsa_ e o efeito não é acionado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito político |Nenhum combina |Ambos os elementos de matriz avaliam como verdadeiros (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1,1), pelo que a condição **notEquals** é _verdadeira_ e o efeito é desencadeado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito político |Um ou mais fósforo |Um elemento de matriz avalia como falso (127.0.0.1 != 127.0.0.1) e um como verdadeiro (127.0.0.1 != 192.168.1.1), pelo que a condição **notEquals** é _falsa_. O operador lógico avalia como verdadeiro **(não** _falso),_ pelo que o efeito é desencadeado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nenhumas |Um ou mais fósforo |Ambos os elementos de matriz avaliam como verdadeiros (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1,1), pelo que a condição **notEquals** é _verdadeira_. O operador lógico avalia como falso **(não** _é verdade),_ para que o efeito não seja desencadeado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito político |Nem todos combinam. |Um elemento de matriz avalia como verdadeiro (127.0.0.1 == 127.0.0.1) e um como falso (127.0.0.0.1 == 192.168.1.1), pelo que a condição **de Iguais** é _falsa_. O operador lógico avalia como verdadeiro **(não** _falso),_ pelo que o efeito é desencadeado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito político |Nem todos combinam. |Ambos os elementos de matriz avaliam como falso (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), pelo que a condição **de Iguais** é _falsa._ O operador lógico avalia como verdadeiro **(não** _falso),_ pelo que o efeito é desencadeado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nenhumas |Todos os fósforos |Um elemento de matriz avalia como verdadeiro (127.0.0.1 == 127.0.0.1) e um como falso (127.0.0.1 == 192.168.1.1), pelo que a condição **equal é** _falsa_ e o efeito não é desencadeado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nenhumas |Todos os fósforos |Ambos os elementos de matriz avaliam como falso (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1, pelo que a condição **de Iguais** é _falsa_ e o efeito não é acionado. |

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [remediar recursos não conformes.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)

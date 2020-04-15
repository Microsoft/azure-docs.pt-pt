---
title: Detalhes da estrutura de definição de políticas
description: Descreve como as definições políticas são usadas para estabelecer convenções para os recursos azure na sua organização.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 017878c4c47a5645ea8815580d2176c7a2ff5d66
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314021"
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição do Azure Policy

A Política Azure estabelece convenções de recursos. As definições de política descrevem [as condições](#conditions) de conformidade dos recursos e o efeito a tomar se uma condição for satisfeita. Uma condição compara um [campo](#fields) de propriedade de recursos a um valor exigido. Os campos de propriedade de recursos são acedidos usando [pseudónimos.](#aliases) Um campo de propriedade de recursos é um campo de valor único ou um [conjunto](#understanding-the--alias) de vários valores. A avaliação da condição é diferente nas matrizes.
Saiba mais sobre [as condições.](#conditions)

Ao definir convenções, pode controlar os custos e gerir mais facilmente os seus recursos. Por exemplo, pode especificar que apenas certos tipos de máquinas virtuais são permitidas. Ou, pode exigir que todos os recursos tenham uma etiqueta particular. As políticas são herdadas por todos os recursos infantis. Se uma política for aplicada a um grupo de recursos, é aplicável a todos os recursos desse grupo de recursos.

O esquema de definição de política encontra-se aqui:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Usa a JSON para criar uma definição de política. A definição de política contém elementos para:

- mode
- parâmetros
- nome de exibição
- descrição
- regra política
  - avaliação lógica
  - efeito

Por exemplo, a Seguinte JSON mostra uma política que limita onde os recursos são implantados:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Todas as amostras da Política Azure estão em amostras da [Política Azure.](../samples/index.md)

## <a name="mode"></a>Modo

**O modo** é configurado dependendo se a apólice está direcionada para uma propriedade do Gestor de Recursos Azure ou uma propriedade do Fornecedor de Recursos.

### <a name="resource-manager-modes"></a>Modos Gestor de Recursos

O **modo** determina quais os tipos de recursos que serão avaliados para uma política. Os modos suportados são:

- `all`: avaliar grupos de recursos e todos os tipos de recursos
- `indexed`: apenas avaliar tipos de recursos que suportem tags e localização

Por exemplo, `Microsoft.Network/routeTables` o recurso suporta etiquetas e localização e é avaliado em ambos os modos. No entanto, o recurso `Microsoft.Network/routeTables/routes` não pode ser `Indexed` marcado e não é avaliado em modo.

Recomendamos que ajuste `all` o **modo** na maioria dos casos. Todas as definições de política `all` criadas através do portal utilizam o modo. Se utilizar o PowerShell ou o Azure CLI, pode especificar manualmente o parâmetro de **modo.** Se a definição de política não incluir um `all` valor de **modo,** não se aplica a Azure PowerShell e ao `null` Azure CLI. Um `null` modo é o `indexed` mesmo que usar para suportar a compatibilidade para trás.

`indexed`deve ser utilizado na criação de políticas que apliquem etiquetas ou locais. Apesar de não ser necessário, evita que recursos que não suportem etiquetas e locais apareçam como incompatíveis nos resultados de conformidade. A exceção são **os grupos de recursos.** As políticas que impõem a localização ou `all` as etiquetas `Microsoft.Resources/subscriptions/resourceGroups` num grupo de recursos devem definir **o modo** e especificamente direcionar o tipo. Por exemplo, consulte [Impor etiquetas](../samples/enforce-tag-rg.md)de grupo de recursos . Para obter uma lista de recursos que suportem tags, consulte o suporte da [Tag para os recursos Azure.](../../../azure-resource-manager/management/tag-support.md)

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Modos de Fornecedor de Recursos (pré-visualização)

Os seguintes modos de Fornecedor de Recursos são atualmente suportados durante a pré-visualização:

- `Microsoft.ContainerService.Data`para a gestão das regras do controlador de admissão no [Serviço Azure Kubernetes.](../../../aks/intro-kubernetes.md) As políticas que utilizam este modo de Fornecedor de Recursos **devem** utilizar o efeito [EnforceRegoPolicy.](./effects.md#enforceregopolicy)
- `Microsoft.Kubernetes.Data`para gerir clusters de kubernetes de motor AKS autogeridos em Azure.
  As políticas que utilizam este modo de fornecedor de recursos **devem** utilizar o efeito [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
- `Microsoft.KeyVault.Data`para gestão de cofres e certificados no [Cofre de Chaves Azure.](../../../key-vault/key-vault-overview.md)

> [!NOTE]
> Os modos de Fornecedor de Recursos apenas suportam definições políticas incorporadas e não suportam iniciativas durante a pré-visualização.

## <a name="parameters"></a>Parâmetros

Os parâmetros ajudam a simplificar a sua gestão política reduzindo o número de definições políticas. Pense em parâmetros como os `name`campos `address` `city`de `state`uma forma , , , . Estes parâmetros mantêm-se sempre os mesmos, no entanto os seus valores mudam com base no preenchimento individual do formulário.
Os parâmetros funcionam da mesma forma quando se constrói políticas. Ao incluir parâmetros numa definição de política, pode reutilizar essa política para diferentes cenários utilizando diferentes valores.

> [!NOTE]
> Os parâmetros podem ser adicionados a uma definição existente e atribuída. O novo parâmetro deve incluir a propriedade **defaultValue.** Isto impede que as atribuições existentes da política ou iniciativa sejam indiretamente inválidas.

### <a name="parameter-properties"></a>Propriedades do parâmetro

Um parâmetro tem as seguintes propriedades que são utilizadas na definição de política:

- **nome**: O nome do seu parâmetro. Usado pela `parameters` função de implantação dentro da regra da política. Para mais informações, consulte [a utilização de um valor de parâmetro](#using-a-parameter-value).
- `type`: Determinar se o parâmetro é uma **corda,** **matriz,** **objeto,** **booleano,** **inteiro,** **flutuador,** ou **data.**
- `metadata`: Define subpropriedades utilizadas principalmente pelo portal Azure para apresentar informações fáceis de utilizar:
  - `description`: A explicação para o que o parâmetro é utilizado. Pode ser usado para fornecer exemplos de valores aceitáveis.
  - `displayName`: O nome amigável mostrado no portal para o parâmetro.
  - `version`: (Opcional) rastreia detalhes sobre a versão do conteúdo de uma definição de política.

    > [!NOTE]
    > O serviço De `version`Política `preview`Azure utiliza, e `deprecated` propriedades para transmitir o nível de mudança para uma definição ou iniciativa e estado integrados. O formato `version` `{Major}.{Minor}.{Patch}`é: . Estados específicos, tais como depreciados ou _pré-visualização,_ são anexados à `version` propriedade ou em outra propriedade como **booleano**. _deprecated_

  - `category`: (Opcional) Determina em que categoria no portal Azure é apresentada a definição de política.
  - `strongType`: (Opcional) Utilizado ao atribuir a definição de política através do portal. Fornece uma lista de conhecimento de contexto. Para mais informações, consulte [strongType](#strongtype).
  - `assignPermissions`: (Opcional) Definido como _verdadeiro_ para que o portal Azure crie atribuições de funções durante a atribuição de políticas. Esta propriedade é útil caso deseje atribuir permissões fora do âmbito de atribuição. Há uma função de atribuição por definição de papel na política (ou por definição de papel em todas as políticas da iniciativa). O valor do parâmetro deve ser um recurso ou âmbito válido.
- `defaultValue`: (Opcional) Define o valor do parâmetro numa atribuição se não for dado qualquer valor.
  Necessário para atualizar uma definição de política existente que é atribuída.
- `allowedValues`: (Opcional) fornece uma série de valores que o parâmetro aceita durante a atribuição.

Como exemplo, poderia definir uma definição de política para limitar os locais onde os recursos podem ser implantados. Um parâmetro para essa definição de política poderia ser **permitidoLocalizações**. Este parâmetro seria utilizado por cada atribuição da definição de política para limitar os valores aceites. O uso de **strongType** proporciona uma experiência melhorada ao concluir a atribuição através do portal:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Usando um valor de parâmetro

Na regra da política, refere os `parameters` parâmetros com a seguinte sintaxe de função:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Esta amostra refere o parâmetro **de localização permitido** que foi demonstrado nas propriedades dos [parâmetros](#parameter-properties).

### <a name="strongtype"></a>strongType

Dentro `metadata` da propriedade, pode utilizar **o strongType** para fornecer uma lista multi-selecionada de opções dentro do portal Azure. **strongType** pode ser um tipo de _recurso_ suportado ou um valor permitido. Para determinar se um _tipo_ de recurso é válido para **o strongType,** utilize [o Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Alguns tipos de _recursos_ não devolvidos pela **Get-AzResourceProvider** são suportados. Estes são:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

O _tipo não-recurso_ permitido valores para **o strongType** são:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Localização da definição

Ao criar uma iniciativa ou política, é necessário especificar a localização da definição. A definição de localização deve ser um grupo de gestão ou uma subscrição. Este local determina o âmbito a que a iniciativa ou a política podem ser atribuídas. Os recursos devem ser membros diretos ou crianças dentro da hierarquia da definição de localização para o alvo para a atribuição.

Se a definição de localização for:

- **Subscrição** - Só podem ser atribuídos recursos nessa subscrição a política.
- **Grupo de gestão** - Só podem ser atribuídos recursos dentro de grupos de gestão de crianças e subscrições infantis. Se pretende aplicar a definição de política a várias subscrições, a localização deve ser um grupo de gestão que contenha essas subscrições.

## <a name="display-name-and-description"></a>Nome e descrição do mostrador

Utiliza o **displayName** e **descrição** para identificar a definição de política e fornecer contexto para quando é utilizado. **displayName** tem um comprimento máximo de _128_ caracteres e **descrição** de um comprimento máximo de _512_ caracteres.

> [!NOTE]
> Durante a criação ou atualização de uma definição de política, **id,** **tipo,** e **nome** são definidos por propriedades externas ao JSON e não são necessários no ficheiro JSON. Buscar a definição de política via SDK devolve as propriedades **id**, **type**, e **nome** como parte do JSON, mas cada uma delas são informações apenas de leitura relacionadas com a definição de política.

## <a name="policy-rule"></a>Regra política

A regra política consiste em **Blocos Se** e **Depois.** No bloco **Se,** define uma ou mais condições que especificam quando a política é aplicada. Pode aplicar operadores lógicos a estas condições para definir com precisão o cenário para uma política.

No bloco **Then,** define-se o efeito que acontece quando as condições **se** encontram preenchidas.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Operadores lógicos

Os operadores lógicos apoiados são:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

A **não** sintaxe inverte o resultado da circunstância. A sintaxe **(semelhante** à lógica **E** operação) requer que todas as condições sejam verdadeiras. A sintaxe **(semelhante** à operação lógica **ou** lógica) requer que uma ou mais condições sejam verdadeiras.

Pode nidificar operadores lógicos. O exemplo que se segue mostra uma **operação não** que está aninhada dentro de uma operação **tudo o** que está a funcionar.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Condições

Uma condição avalia se um **campo** ou o acessório de **valor** satisfaz determinados critérios. As condições suportadas são:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` | `"greaterOrEquals": intValue`
- `"exists": "bool"`

Para **menos**, **lessOrEquals**, **maiores**, e **maiorEsOrEquals**, se o tipo de propriedade não corresponder ao tipo de condição, um erro é lançado. As comparações `InvariantCultureIgnoreCase`de cordas são feitas usando .

Ao utilizar as condições **similares** e `*` **não semelhantes,** fornece um wildcard no valor.
O valor não deve ter `*`mais do que um wildcard.

Ao utilizar as condições do **jogo** e `?` **não** match, forneça `#` combinar um dígito, para uma letra, `.` para combinar com qualquer personagem, e qualquer outro personagem que corresponda a esse personagem real. Embora, **match** e **notMatch** sejam sensíveis a casos, todas as outras condições que avaliam um _stringValue_ são insensíveis a casos. Alternativas insensíveis a casos estão disponíveis em **matchInsensitive** e **não MatchInsensitively**.

Num valor de campo de matriz ** \] de pseudónimos, cada elemento da matriz é avaliado individualmente com elementos lógicos e entre elementos. \[ \* ** **and** Para mais informações, consulte [Avaliar \[ \* \] o pseudónimo.](../how-to/author-policies-for-arrays.md#evaluating-the--alias)

### <a name="fields"></a>Campos

As condições formam-se através da utilização de campos. Um campo corresponde às propriedades na carga útil do pedido de recursos e descreve o estado do recurso.

São apoiados os seguintes campos:

- `name`
- `fullName`
  - Devolve o nome completo do recurso. O nome completo de um recurso é o nome de recurso preparado por quaisquer nomes de recursos-mãe (por exemplo"myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Use **global** para recursos que são agnósticos de localização.
- `identity.type`
  - Devolve o tipo de [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md) ativada no recurso.
- `tags`
- `tags['<tagName>']`
  - Esta sintaxe de suporte suporta nomes de etiquetas que tenham pontuação como hífen, período ou espaço.
  - Onde ** \<tagName\> ** é o nome da etiqueta para validar a condição para.
  - Exemplos: `tags['Acct.CostCenter']` onde **Acct.CostCenter** é o nome da etiqueta.
- `tags['''<tagName>''']`
  - Esta sintaxe de suporte suporta nomes de etiquetas que têm apóstrofos nele escapando com apóstrofos duplos.
  - Onde **\<'\>tagName '** é o nome da etiqueta para validar a condição para.
  - Exemplo: `tags['''My.Apostrophe.Tag''']` onde **'My.Apostrophe.Tag'** é o nome da etiqueta.
- pseudónimos de propriedade - para uma lista, ver [Pseudónimos](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`e `tags[tag.with.dots]` ainda são formas aceitáveis de declarar um campo de etiquetas. No entanto, as expressões preferidas são as acima enumeradas.

#### <a name="use-tags-with-parameters"></a>Utilize etiquetas com parâmetros

Um valor de parâmetro pode ser passado para um campo de etiquetas. A passagem de um parâmetro para um campo de identificação aumenta a flexibilidade da definição de política durante a atribuição de políticas.

No exemplo seguinte, `concat` é usado para criar um lookup de campo de tags para a etiqueta chamada o valor do parâmetro **tagName.** Se essa etiqueta não existir, o efeito **modificador** é usado para adicionar a etiqueta utilizando o valor da `resourcegroup()` mesma etiqueta nomeada definida no grupo de recursos parentais de recursos auditados, utilizando a função de procuração.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Valor

As condições também podem ser formadas usando **o valor**. **verificações de valor** contra [parâmetros,](#parameters) [funções de modelo suportado,](#policy-functions)ou literais.
**o valor** é emparelhado com qualquer [condição](#conditions)suportada.

> [!WARNING]
> Se o resultado de uma _função_ de modelo for um erro, a avaliação da política falha. Uma avaliação falhada é um **negação**implícito. Para mais informações, consulte [evitar falhas](#avoiding-template-failures)no modelo . Utilize o modo de [execução](./assignment-structure.md#enforcement-mode) **DoNotEnforce** para evitar o impacto de uma avaliação falhada em recursos novos ou atualizados enquanto testa e valida uma nova definição de política.

#### <a name="value-examples"></a>Exemplos de valor

Este exemplo de **value** regra de política `resourceGroup()` usa valor para comparar o resultado `*netrg`da função e a propriedade de **nome** devolvido a uma condição **semelhante** de . A regra nega qualquer recurso `Microsoft.Network/*` que não seja do **tipo** em qualquer grupo de recursos cujo nome termina em `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Este exemplo de regra de política usa **valor** para verificar se o resultado de múltiplas funções aninhadas **é** `true`igual . A regra nega qualquer recurso que não tenha pelo menos três etiquetas.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Evitando falhas no modelo

O uso de _funções_ de modelo em **valor** permite muitas funções aninhadas complexas. Se o resultado de uma _função_ de modelo for um erro, a avaliação da política falha. Uma avaliação falhada é um **negação**implícito. Um exemplo de **valor** que falha em certos cenários:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

A regra da política de exemplo acima usa [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) para comparar os três primeiros caracteres de **nome** com **o ABC**. Se o **nome** for inferior `substring()` a três caracteres, a função resulta num erro. Este erro faz com que a política se torne um efeito **de negação.**

Em vez disso, utilize a função [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) para verificar se os três primeiros caracteres de **nome** são iguais ao **ABC** sem permitir que um **nome** inferior a três caracteres cause um erro:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Com a regra da `if()` política revista, verifica a `substring()` duração do **nome** antes de tentar obter um valor com menos de três caracteres. Se o **nome** for muito curto, o valor "não começar com o ABC" é devolvido e comparado com o **ABC**. Um recurso com um nome curto que não começa com o **ABC** ainda falha na regra da política, mas já não causa um erro durante a avaliação.

### <a name="count"></a>Contagem

As condições que contam quantos membros de uma matriz na carga útil de recursos satisfazem uma expressão de condição pode ser formada usando a expressão **da contagem.** Os cenários comuns estão a verificar se "pelo menos um de", "exatamente um de", "todos", ou "nenhum dos" membros da matriz satisfazem a condição. **contagem** avalia [ \[ \* \] ](#understanding-the--alias) cada membro da matriz de pseudónimo para uma expressão de condição e resume os _verdadeiros_ resultados, que é então comparado com o operador de expressão.

A estrutura da expressão da **contagem** é:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

As seguintes propriedades são utilizadas com **contagem:**

- **contagem.campo** (necessário): Contém o caminho para a matriz e deve ser um pseudónimo matriz. Se a matriz estiver em falta, a expressão é avaliada como _falsa_ sem considerar a expressão da condição.
- **contagem.onde** (opcional): A expressão de [ \[ \* \] ](#understanding-the--alias) condição para avaliar individualmente cada membro da matriz de conjunto de pseudónimos do **count.field**. Se esta propriedade não for fornecida, todos os membros da matriz com o caminho do 'campo' são avaliados _como verdadeiros_. Qualquer [condição](../concepts/definition-structure.md#conditions) pode ser usada dentro desta propriedade.
  [Os operadores lógicos](#logical-operators) podem ser usados dentro desta propriedade para criar requisitos complexos de avaliação.
- condição (necessária): O valor é comparado com o número de itens que satisfazem a **contagem.onde** a expressão da condição. ** \<\> ** Deve ser utilizada uma [condição](../concepts/definition-structure.md#conditions) numérica.

#### <a name="count-examples"></a>Exemplos de contagem

Exemplo 1: Verifique se uma matriz está vazia

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Exemplo 2: Verifique se apenas um membro da matriz para atender à expressão da condição

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Exemplo 3: Verifique se há pelo menos um membro da matriz para atender à expressão da condição

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Exemplo 4: Verifique se todos os membros da matriz de objetos cumprem a expressão da condição

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Exemplo 5: Verifique se todos os membros da string array cumprem a expressão da condição

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Exemplo 6: Use **o campo** dentro **do valor** para verificar se todos os membros da matriz cumprem a expressão da condição

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Exemplo 7: Verifique se pelo menos um membro da matriz corresponde a várias propriedades na expressão da condição

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Efeito

A Política Azure apoia os seguintes tipos de efeitos:

- **Apêndice**: adiciona o conjunto definido de campos ao pedido
- **Auditoria**: gera um evento de alerta no registo de atividade, mas não falha o pedido
- **AuditoriaIfNotExists:** gera um evento de alerta no registo de atividade se um recurso relacionado não existir
- **Negar**: gera um evento no registo de atividade e falha o pedido
- **DeployIfNotExists:** implanta um recurso relacionado se já não existir
- **Deficientes**: não avalia recursos para o cumprimento da regra política
- **EnforceOPAConstraint** (pré-visualização): configura o controlador de admissão do Agente de Política Aberta com gatekeeper v3 para clusters Kubernetes autogeridos em Azure (pré-visualização)
- **EnforceRegoPolicy** (pré-visualização): configura o controlador de admissão do Agente de Política Aberta com gatekeeper v2 no Serviço Azure Kubernetes
- **Modificar**: adiciona, atualiza ou remove as etiquetas definidas de um recurso

Para obter detalhes completos sobre cada efeito, ordem de avaliação, propriedades e exemplos, consulte [Compreender efeitos políticos do Azure](effects.md).

### <a name="policy-functions"></a>Funções políticas

Todas as [funções](../../../azure-resource-manager/templates/template-functions.md) de modelo do Gestor de Recursos estão disponíveis para utilização dentro de uma regra de política, exceto as seguintes funções e funções definidas pelo utilizador:

- copyIndex()
- implantação()
- lista*
- newGuid()
- pickZones()
- fornecedores()
- referência()
- recursosId()
- variáveis()

> [!NOTE]
> Estas funções ainda estão disponíveis dentro da `details.deployment.properties.template` parte da implementação do modelo numa definição de política **deployIfNotExists.**

A seguinte função está disponível para ser utilizada numa regra de política, mas difere da utilização num modelo de Gestor de Recursos Azure:

- `utcNow()`- Ao contrário de um modelo de Gestor de Recursos, este pode ser usado fora do padrãoValue.
  - Devolve uma corda que está definida para a data e hora atuais no formato DataTime universal 'yyyy-MM-ddTHH:mm:ss.fffffffZ'

As seguintes funções só estão disponíveis nas regras políticas:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dataTempo**: [Obrigatório] string - String in the Universal ISO 8601 DateTime formato 'yyyy-MM-ddTHH:mm:ss.fffffffZ'
  - **númeroOfDaysToAdd**: [Obrigatório] inteiro - Número de dias para adicionar
- `field(fieldName)`
  - **nome**de campo : [Obrigatório] string - Nome do [campo](#fields) para recuperar
  - Devolve o valor desse campo a partir do recurso que está a ser avaliado pela condição Se
  - `field`é usado principalmente com **AuditIfNotExists** e **DeployIfNotExists** para campos de referência no recurso que estão a ser avaliados. Um exemplo desta utilização pode ser visto no [exemplo DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Devolve a versão API do pedido que desencadeou a avaliação política (exemplo: `2019-09-01`).
    Esta será a versão API que foi utilizada no pedido de avaliação PUT/PATCH sobre criação/atualização de recursos. A versão Mais recente da API é sempre utilizada durante a avaliação de conformidade dos recursos existentes.
  
#### <a name="policy-function-example"></a>Exemplo de função de política

Este exemplo de `resourceGroup` regra de política usa a função de recurso para `like` obter a propriedade do **nome,** combinada com a `concat` função de matriz e objeto para construir uma condição que impõe o nome do recurso para começar com o nome do grupo de recursos.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliases

Você usa pseudónimos de propriedade para aceder a propriedades específicas para um tipo de recurso. Os pseudónimos permitem-lhe restringir quais os valores ou condições permitidos para uma propriedade num recurso. Cada pseudónimo mapeia caminhos em diferentes versões API para um determinado tipo de recursos. Durante a avaliação política, o motor de política obtém o caminho da propriedade para a versão API.

A lista de pseudónimos está sempre a crescer. Para encontrar quais pseudónimos são atualmente apoiados pela Política Azure, utilize um dos seguintes métodos:

- Extensão da Política Azure para Código de Estúdio Visual (recomendado)

  Utilize a [extensão da Política Azure para](../how-to/extension-for-vscode.md) o Código do Estúdio Visual para visualizar e descobrir pseudónimos para propriedades de recursos.

  ![Extensão da Política Azure para Código de Estúdio Visual](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Utilize `project` o operador para exibir o **pseudónimo** de um recurso.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- CLI do Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Compreender o pseudónimo [*]

Vários dos pseudónimos disponíveis têm uma versão que aparece como um nome ** \[ \* ** 'normal' e outra que lhe tem anexado. Por exemplo:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

O pseudónimo 'normal' representa o campo como um único valor. Este campo é para cenários exatos de comparação de correspondência quando todo o conjunto de valores deve ser exatamente como definido, nem mais nem menos.

** \[ O \* ** pseudónimo permite comparar com o valor de cada elemento na matriz e propriedades específicas de cada elemento. Esta abordagem permite comparar propriedades de elementos para cenários "se nenhum", "se algum", ou "se todos". Para cenários mais complexos, utilize a expressão da condição da [contagem.](#count) Utilizando **o\[\*IPRules**, um exemplo seria validar que cada _ação_ é _A Deny,_ mas não se preocupando com quantas regras existem ou qual é o _valor_ IP.
Esta regra da amostra verifica quaisquer correspondências do **\[\*\]ipRules .value** to **10.0.4.1** e aplica o **efeitoType** apenas se não encontrar pelo menos uma correspondência:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Para mais informações, consulte [a avaliação do []\*pseudónimo](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Iniciativas

As iniciativas permitem-lhe agrupar várias definições políticas relacionadas para simplificar as atribuições e a gestão porque trabalha com um grupo como um único item. Por exemplo, pode agrupar definições de políticas de marcação relacionadas numa única iniciativa. Em vez de atribuir cada política individualmente, aplica-se a iniciativa.

> [!NOTE]
> Uma vez atribuída uma iniciativa, os parâmetros de nível de iniciativa não podem ser alterados. Devido a isto, a recomendação é definir um **Valor padrão** ao definir o parâmetro.

O exemplo que se segue ilustra como criar `costCenter` uma `productName`iniciativa de manuseamento de duas etiquetas: e . Usa duas políticas incorporadas para aplicar o valor da etiqueta padrão.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Veja [Compreender os efeitos do Policy](effects.md).
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)

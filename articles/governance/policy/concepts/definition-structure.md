---
title: Detalhes da estrutura de definição de políticas
description: Descreve como as definições de política são usadas para estabelecer convenções para recursos Azure na sua organização.
ms.date: 10/22/2020
ms.topic: conceptual
ms.openlocfilehash: 52adaf9522e4690c4c44a72ed47592f5b1d6471e
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883253"
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição do Azure Policy

A Política Azure estabelece convenções para os recursos. As definições de política descrevem [as condições](#conditions) de conformidade dos recursos e o efeito a ter em conta se uma condição for satisfeita. Uma condição compara um [campo](#fields) de propriedade de recurso ou um [valor](#value) a um valor exigido. Os campos de propriedade de recursos são acedidos através [de pseudónimos.](#aliases) Quando um campo de propriedade de recursos é uma matriz, um pseudónimo de matriz especial pode ser usado para selecionar valores de todos os [membros](#understanding-the--alias) da matriz e aplicar uma condição a cada um.
Saiba mais sobre [as condições.](#conditions)

Ao definir convenções, pode controlar os custos e gerir mais facilmente os seus recursos. Por exemplo, pode especificar que apenas certos tipos de máquinas virtuais são permitidos. Ou pode exigir que os recursos tenham uma etiqueta particular. As atribuições políticas são herdadas por recursos infantis. Se uma atribuição de política for aplicada a um grupo de recursos, é aplicável a todos os recursos desse grupo de recursos.

A política de definição _de políticaRule_ schema é encontrada aqui: [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

Usas o JSON para criar uma definição de política. A definição de política contém elementos para:

- nome de exibição
- descrição
- mode
- do IdP
- parâmetros
- regra política
  - avaliação lógica
  - efeito

Por exemplo, o seguinte JSON mostra uma política que limita onde os recursos são utilizados:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

Azure Policy incorporados e padrões estão em [amostras da Política Azure](../samples/index.md).

## <a name="display-name-and-description"></a>Nome e descrição do visor

Usa **o nome e** **a descrição** do display Para identificar a definição de política e fornecer contexto para quando for usado. **displayName** tem um comprimento máximo de _128_ caracteres e **descrição** de um comprimento máximo de _512_ caracteres.

> [!NOTE]
> Durante a criação ou atualização de uma definição de política, **id,** **tipo** e **nome** são definidos por propriedades externas ao JSON e não são necessários no ficheiro JSON. A obtenção da definição de política via SDK devolve o **id**, **tipo**, e propriedades de **nome** como parte do JSON, mas cada uma delas são apenas informações de leitura relacionadas com a definição de política.

## <a name="type"></a>Tipo

Embora a propriedade **tipo** não possa ser definida, existem três valores que são devolvidos pela SDK e visíveis no portal:

- `Builtin`: Estas definições de política são fornecidas e mantidas pela Microsoft.
- `Custom`: Todas as definições de política criadas pelos clientes têm este valor.
- `Static`: Indica uma definição de política [de conformidade regulamentar](./regulatory-compliance.md) com a Propriedade **da** Microsoft . Os resultados de conformidade destas definições de política são os resultados de auditorias de terceiros sobre a infraestrutura da Microsoft. No portal Azure, este valor é por vezes apresentado à medida **que a Microsoft conseguiu**. Para mais informações, consulte [responsabilidade partilhada na nuvem.](../../../security/fundamentals/shared-responsibility.md)

## <a name="mode"></a>Modo

**O modo** está configurado dependendo se a política está direcionada a uma propriedade do Azure Resource Manager ou de uma propriedade do Fornecedor de Recursos.

### <a name="resource-manager-modes"></a>Modos gestor de recursos

O **modo** determina quais os tipos de recursos avaliados para uma definição de política. Os modos suportados são:

- `all`: avaliar grupos de recursos, subscrições e todos os tipos de recursos
- `indexed`: apenas avaliar tipos de recursos que suportam etiquetas e localização

Por exemplo, o recurso `Microsoft.Network/routeTables` suporta tags e localização e é avaliado em ambos os modos. No entanto, o recurso `Microsoft.Network/routeTables/routes` não pode ser marcado e não é avaliado em `Indexed` modo.

Recomendamos que desfase o **modo** `all` na maioria dos casos. Todas as definições de política criadas através do portal utilizam o `all` modo. Se utilizar o PowerShell ou o Azure CLI, pode especificar manualmente o parâmetro do **modo.** Se a definição de política não incluir um valor de **modo,** ele falha `all` em Azure PowerShell e `null` em Azure CLI. Um `null` modo é o mesmo que usar para suportar a `indexed` retrocompatibilidade.

`indexed` deve ser usado na criação de políticas que imponham tags ou locais. Embora não seja necessário, impede que recursos que não suportam tags e locais apareçam como incompatíveis nos resultados de conformidade. A exceção são **grupos de recursos** e **subscrições.** As definições de política que impõem a localização ou as tags num grupo de recursos ou subscrição devem definir **o modo** `all` de e especificamente direcionar o `Microsoft.Resources/subscriptions/resourceGroups` ou o `Microsoft.Resources/subscriptions` tipo. Por exemplo, consulte [Padrão: Etiquetas - Amostra #1](../samples/pattern-tags.md). Para obter uma lista de recursos que suportem tags, consulte [o suporte da Tag para os recursos da Azure.](../../../azure-resource-manager/management/tag-support.md)

### <a name="resource-provider-modes"></a>Modos fornecedores de recursos

O seguinte modo fornecedor de recursos é totalmente suportado:

- `Microsoft.Kubernetes.Data` para gerir os seus clusters Kubernetes em ou fora de Azure. Definições que utilizam este modo de fornecedor de recursos utilizam _auditoria_ de efeitos, _negar_ e _desativar_. A utilização do efeito [EnforceOPAConstraint](./effects.md#enforceopaconstraint) é _depreciada._

Os seguintes modos de Fornecedor de Recursos são atualmente suportados como **pré-visualização:**

- `Microsoft.ContainerService.Data` para gerir as regras do controlador de admissão no [Serviço Azure Kubernetes](../../../aks/intro-kubernetes.md). As definições que utilizam este modo fornecedor de recursos **devem** utilizar o efeito [EnforceRegoPolicy.](./effects.md#enforceregopolicy) Este modo é _precotado._
- `Microsoft.KeyVault.Data` para a gestão de cofres e certificados em [Azure Key Vault](../../../key-vault/general/overview.md). Para obter mais informações sobre estas definições políticas, consulte [Integrar o Cofre da Chave Azure com a Política Azure](../../../key-vault/general/azure-policy.md).

> [!NOTE]
> Os modos fornecedores de recursos apenas suportam definições políticas incorporadas e não suportam [isenções](./exemption-structure.md).

## <a name="metadata"></a>Metadados

A propriedade opcional `metadata` armazena informações sobre a definição de política. Os clientes podem definir quaisquer propriedades e valores úteis à sua organização `metadata` em. No entanto, existem algumas propriedades _comuns_ usadas pela Azure Policy e em incorporados.

### <a name="common-metadata-properties"></a>Propriedades comuns de metadados

- `version` (cadeia): Rastreia detalhes sobre a versão do conteúdo de uma definição de política.
- `category` (cadeia): Determina em que categoria no portal Azure é apresentada a definição de política.
- `preview` (boolean: bandeira verdadeira ou falsa para se a definição de política for _de visualização_.
- `deprecated` (boolean: verdadeira ou falsa bandeira para se a definição de política tiver sido marcada como _prevada_.

> [!NOTE]
> O serviço Azure Policy `version` utiliza, `preview` e propriedades para transmitir `deprecated` o nível de mudança a uma definição ou iniciativa e iniciativa de política incorporada. O formato `version` de: `{Major}.{Minor}.{Patch}` . Estados específicos, tais como _prevadidos_ ou _pré-visualização,_ são anexados à `version` propriedade ou em outra propriedade como um **booleano**. Para obter mais informações sobre a forma como as versões Azure Policy incorporadas, consulte [a versão incorporada.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)

## <a name="parameters"></a>Parâmetros

Os parâmetros ajudam a simplificar a sua gestão de políticas reduzindo o número de definições políticas. Pense em parâmetros como os campos numa forma , `name` `address` `city` `state` . Estes parâmetros mantêm-se sempre os mesmos, no entanto os seus valores mudam com base no indivíduo que preenche o formulário.
Os parâmetros funcionam da mesma forma quando se constrói políticas. Ao incluir parâmetros numa definição de política, pode reutilizar essa política para diferentes cenários utilizando valores diferentes.

> [!NOTE]
> Os parâmetros podem ser adicionados a uma definição existente e atribuída. O novo parâmetro deve incluir a propriedade **padrãoValue.** Isto impede que as atribuições existentes da política ou iniciativa sejam indiretamente inválidas.

### <a name="parameter-properties"></a>Propriedades de parâmetros

Um parâmetro tem as seguintes propriedades que são usadas na definição de política:

- `name`: O nome do seu parâmetro. Utilizado pela `parameters` função de implantação dentro da regra da política. Para obter mais informações, consulte [utilizando um valor de parâmetro.](#using-a-parameter-value)
- `type`: Determina se o parâmetro é uma **corda,** **matriz,** **objeto,** **boolean,** **inteiro,** **boia** ou **data**.
- `metadata`: Define subpropriedades utilizadas principalmente pelo portal Azure para apresentar informações fáceis de utilizar:
  - `description`: A explicação para o que o parâmetro é usado. Pode ser usado para fornecer exemplos de valores aceitáveis.
  - `displayName`: O nome amigável indicado no portal para o parâmetro.
  - `strongType`: (Opcional) Utilizado ao atribuir a definição de política através do portal. Fornece uma lista de conscientes de contexto. Para obter mais informações, consulte [o StrongType](#strongtype).
  - `assignPermissions`: (Opcional) Definir como _verdadeiro_ para que o portal Azure crie atribuições de funções durante a atribuição de políticas. Esta propriedade é útil no caso de pretender atribuir permissões fora do âmbito de atribuição. Há uma atribuição de papel por definição de papel na política (ou por definição de função em todas as políticas da iniciativa). O valor do parâmetro deve ser um recurso ou âmbito válido.
- `defaultValue`: (Opcional) Define o valor do parâmetro numa atribuição se não for dado qualquer valor.
  Necessário para atualizar uma definição de política existente que é atribuída.
- `allowedValues`: (Opcional) Fornece uma matriz de valores que o parâmetro aceita durante a atribuição.

Como exemplo, pode definir uma definição de política para limitar os locais onde os recursos podem ser implantados. Um parâmetro para essa definição de política poderia ser **permitido.** Este parâmetro seria utilizado por cada atribuição da definição de política para limitar os valores aceites. A utilização de **StrongType** proporciona uma experiência melhorada ao completar a atribuição através do portal:

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

Na regra política, refere-se a parâmetros com a seguinte `parameters` sintaxe de função:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Esta amostra refere o parâmetro **permitido delocalização** que foi demonstrado em [propriedades de parâmetros](#parameter-properties).

### <a name="strongtype"></a>ForteType

Dentro da `metadata` propriedade, você pode usar **o StrongType** para fornecer uma lista multi-selecionada de opções dentro do portal Azure. **StrongType** pode ser um tipo de _recurso_ suportado ou um valor permitido. Para determinar se um _tipo de recurso_ é válido para o **StrongType,** utilize [o Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider). O formato para um _tipo de recurso_ **forteType** é `<Resource Provider>/<Resource Type>` . Por exemplo, `Microsoft.Network/virtualNetworks/subnets`.

Alguns tipos de _recursos_ não devolvidos pela **Get-AzResourceProvider** são suportados. Estes tipos são:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

O _tipo_ de não recurso permitiu valores para **o Tipo forte** são:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Localização de definição

Ao criar uma iniciativa ou política, é necessário especificar o local da definição. A localização da definição deve ser um grupo de gestão ou uma subscrição. Este local determina o âmbito a que a iniciativa ou a política podem ser atribuídas. Os recursos devem ser membros diretos ou crianças dentro da hierarquia do local de definição a destino para a atribuição.

Se a localização da definição for:

- **Subscrição** - Apenas os recursos dentro dessa subscrição podem ser atribuídos a definição de política.
- **Grupo de gestão** - Apenas os recursos dentro de grupos de gestão infantil e assinaturas infantis podem ser atribuídos a definição de política. Se pretende aplicar a definição de política a várias subscrições, a localização deve ser um grupo de gestão que contenha cada subscrição.

Para mais informações, consulte [o âmbito de aplicação da Política Azure.](./scope.md#definition-location)

## <a name="policy-rule"></a>Regra política

A regra da política consiste em **Blocos De E** **depois.** No Bloco **Se,** define uma ou mais condições que especificam quando a apólice é aplicada. Pode aplicar operadores lógicos nestas condições para definir com precisão o cenário de uma política.

No bloco **Then,** define-se o efeito que acontece quando as condições **'Se'** estão preenchidas.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Operadores lógicos

Os operadores lógicos apoiados são:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

A **não** sintaxe inverte o resultado da circunstância. A sintaxe **allOf** (semelhante à lógica **e** operação) requer que todas as condições sejam verdadeiras. A **sintaxe qualquer** (semelhante à lógica **ou** operação) requer que uma ou mais condições sejam verdadeiras.

Pode nidificar operadores lógicos. O exemplo a seguir mostra uma **operação não** aninhada no âmbito de uma operação **allOf.**

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

Uma condição avalia se um **campo** ou o acessório **de valor** cumpre determinados critérios. As condições apoiadas são:

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
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

Por **menos,** **menos, os locais EQuals**, **maiores** e **maiores,** se o tipo de propriedade não corresponder ao tipo de condição, é lançado um erro. As comparações de cordas são feitas `InvariantCultureIgnoreCase` utilizando.

Ao utilizar as condições **similares** e **não semelhantes,** fornece um wildcard `*` no valor.
O valor não deve ter mais do que um `*` wildcard.

Ao utilizar as condições **de jogo** e não **de jogo,** forneça `#` para combinar um dígito, para uma `?` letra, para combinar com `.` qualquer personagem, e qualquer outro personagem que corresponda a esse personagem real. Embora **o match** e **o notMatch** sejam sensíveis a casos, todas as outras condições que avaliam uma _cadeiaValue_ são insensíveis a caso. Alternativas insensíveis a casos estão disponíveis em **matchInsensitively** e **não MatchInsensitively**.

Num valor de campo de matriz **\[ \* \] de pseudónimo,** cada elemento na matriz é avaliado individualmente com elementos lógicos **e** entre elementos. Para obter mais informações, consulte as propriedades dos recursos da matriz de [referência.](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)

### <a name="fields"></a>Campos

As condições são formadas através da utilização de campos. Um campo corresponde às propriedades na carga útil do pedido de recurso e descreve o estado do recurso.

São suportados os seguintes campos:

- `name`
- `fullName`
  - Devolve o nome completo do recurso. O nome completo de um recurso é o nome de recurso pré-reservado por quaisquer nomes de recursos-mãe (por exemplo" myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Use **global** para recursos que são agnósticos de localização.
- `id`
  - Devolve o ID de recursos do recurso que está a ser avaliado.
  - Exemplo: `/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
- `identity.type`
  - Devolve o tipo de [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md) ativada no recurso.
- `tags`
- `tags['<tagName>']`
  - Esta sintaxe de suporte suporta nomes de etiquetas que têm pontuação como um hífen, período ou espaço.
  - Onde **\<tagName\>** está o nome da etiqueta para validar a condição para.
  - Exemplos: `tags['Acct.CostCenter']` onde **Acct.CostCenter** é o nome da etiqueta.
- `tags['''<tagName>''']`
  - Esta sintaxe de suporte suporta nomes de etiquetas que têm apóstrofos nele escapando com apóstrofos duplos.
  - Onde **' \<tagName\> ' é** o nome da etiqueta para validar a condição para.
  - Exemplo: `tags['''My.Apostrophe.Tag''']` onde **'My.Apostrophe.Tag'** é o nome da etiqueta.
- pseudónimos imobiliários - para uma lista, ver [Aliases](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` e ainda são formas `tags[tag.with.dots]` aceitáveis de declarar um campo de tags. No entanto, as expressões preferidas são as acima referidas.

#### <a name="use-tags-with-parameters"></a>Use etiquetas com parâmetros

Um valor de parâmetro pode ser passado para um campo de identificação. Passar um parâmetro para um campo de etiquetas aumenta a flexibilidade da definição de política durante a atribuição de políticas.

No exemplo seguinte, `concat` é usado para criar um lookup de campo de tags para a etiqueta nomeado o valor do parâmetro **tagName.** Se essa etiqueta não existir, o efeito **modificação** é utilizado para adicionar a etiqueta utilizando o valor da mesma etiqueta definida no grupo de recursos auditados dos recursos parentais utilizando a `resourcegroup()` função de procura.

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

As condições também podem ser formadas com **o valor.** **valore** verifica as condições contra [parâmetros,](#parameters) [funções de modelo suportadas,](#policy-functions)ou literais. **o valor** é emparelhado com qualquer [condição](#conditions)suportada .

> [!WARNING]
> Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falha. Uma avaliação falhada é uma **negação** implícita. Para obter mais informações, consulte [evitar falhas no modelo](#avoiding-template-failures). Utilize [a aplicação da](./assignment-structure.md#enforcement-mode) **DoNotEnforce** para evitar o impacto de uma avaliação falhada em recursos novos ou atualizados enquanto testa e valida uma nova definição de política.

#### <a name="value-examples"></a>Exemplos de valor

Este exemplo de regra de política usa **valor** para comparar o resultado da `resourceGroup()` função e a propriedade do **nome** devolvido a uma condição **semelhante** de `*netrg` . A regra nega qualquer recurso não do `Microsoft.Network/*` **tipo** em qualquer grupo de recursos cujo nome termine em `*netrg` .

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

Este exemplo de regra de política utiliza **valor** para verificar se o resultado de várias funções aninhadas **é igual** `true` . A regra nega qualquer recurso que não tenha pelo menos três etiquetas.

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

#### <a name="avoiding-template-failures"></a>Evitando falhas de modelo

A utilização de _funções_ de modelo em **valor** permite muitas funções aninhadas complexas. Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falha. Uma avaliação falhada é uma **negação** implícita. Um exemplo de um **valor** que falha em certos cenários:

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

A regra da política de exemplo acima utiliza [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) para comparar os três primeiros caracteres do **nome** com **abc**. Se **o nome** for mais curto do que três caracteres, a `substring()` função resulta num erro. Este erro faz com que a política se torne um efeito **de negação.**

Em vez disso, utilize a função [se()](../../../azure-resource-manager/templates/template-functions-logical.md#if) para verificar se os três primeiros caracteres do **nome** são iguais **a abc** sem permitir que um **nome** inferior a três caracteres cause um erro:

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

Com a regra da política revista, `if()` verifica o comprimento do **nome** antes de tentar obter um valor com menos de `substring()` três caracteres. Se o **nome** for demasiado curto, o valor "não começando pelo ABC" é devolvido e comparado com **o ABC**. Um recurso com um nome curto que não começa com o **ABC** ainda falha a regra da política, mas já não causa um erro durante a avaliação.

### <a name="count"></a>de palavras

As condições que contam quantos membros de uma matriz na carga útil do recurso satisfazem uma expressão da condição podem ser formadas usando a expressão **da contagem.** Cenários comuns estão a verificar se "pelo menos um", "exatamente um dos", "todos", ou "nenhum dos" membros da matriz satisfazem a condição. **contagem** avalia cada membro da matriz [ \[ \* \] de pseudónimo para](#understanding-the--alias) uma expressão de condição e resume os _verdadeiros_ resultados, que é então comparado com o operador de expressão. **As** expressões de contagem podem ser adicionadas até três vezes a uma única definição **de regra de política.**

A estrutura da expressão **da contagem** é:

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

- **count.field** (obrigatório): Contém o caminho para a matriz e deve ser um pseudónimo de matriz. Se faltar a matriz, a expressão é avaliada como _falsa_ sem considerar a expressão da condição.
- **count.where** (opcional): A expressão da condição para avaliar individualmente cada membro da [ \[ \* \]](#understanding-the--alias) matriz de **nomes de conde.field**. Se esta propriedade não for fornecida, todos os membros da matriz com o caminho do 'campo' são avaliados como _verdadeiros_. Qualquer [condição](../concepts/definition-structure.md#conditions) pode ser usada dentro desta propriedade.
  [Os operadores lógicos](#logical-operators) podem ser usados dentro desta propriedade para criar requisitos de avaliação complexos.
- **\<condition\>** (obrigatório): O valor é comparado com o número de itens que cumpriram a **contagem.onde** a expressão da condição. Deve ser utilizada uma [condição](../concepts/definition-structure.md#conditions) numérica.

Para obter mais detalhes sobre como trabalhar com propriedades de matriz na Política Azure, incluindo explicações detalhadas sobre como a expressão da contagem é avaliada, consulte [as propriedades de recursos da matriz de referência](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

#### <a name="count-examples"></a>Contar exemplos

Exemplo 1: Verifique se uma matriz está vazia

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Exemplo 2: Verifique se apenas um membro da matriz satisfaz a expressão da condição

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

Exemplo 3: Verifique se pelo menos um membro da matriz para atender à expressão da condição

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

Exemplo 4: Verifique se todos os membros da matriz de objeto satisfazem a expressão da condição

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

Exemplo 5: Verifique se pelo menos um membro da matriz corresponde a várias propriedades na expressão da condição

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

Exemplo 6: Utilizar `field()` a função dentro das `where` condições de acesso ao valor literal do membro da matriz atualmente avaliado. Esta condição verifica se não existem regras de segurança com um valor _prioritário_ numerado.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
          "value": "[mod(first(field('Microsoft.Network/networkSecurityGroups/securityRules[*].priority')), 2)]",
          "equals": 0
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Efeito

A Azure Policy apoia os seguintes tipos de efeito:

- **Apêndice**: adiciona o conjunto de campos definido ao pedido
- **Auditoria**: gera um evento de alerta no registo de atividades mas não falha o pedido
- **AuditIfNotExists**: gera um evento de alerta no registo de atividades se um recurso relacionado não existir
- **Negar:** gera um evento no registo de atividades e falha o pedido
- **ImplementarIfNotExists**: implementa um recurso relacionado se já não existir
- **Deficiente:** não avalia recursos para o cumprimento da regra da política
- **Modificar:** adicionar, atualizar ou remover as tags definidas de um recurso
- **EnforceOPAConstraint** (preterido): configura o controlador de admissão de Agente de Política Aberta com Gatekeeper v3 para clusters kubernetes auto-geridos em Azure
- **EnforceRegoPolicy** (precotado): configura o controlador de admissões de Agente de Política Aberta com Gatekeeper v2 no Serviço Azure Kubernetes

Para obter detalhes completos sobre cada efeito, ordem de avaliação, propriedades e exemplos, consulte [Understanding Azure Policy Effects](effects.md).

### <a name="policy-functions"></a>Funções políticas

Todas as [funções do modelo do Gestor de Recursos](../../../azure-resource-manager/templates/template-functions.md) estão disponíveis para utilização dentro de uma regra de política, exceto as seguintes funções e funções definidas pelo utilizador:

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
> Estas funções ainda estão disponíveis na `details.deployment.properties.template` parte da implementação do modelo numa definição de política **de implementaçãoIfNotExists.**

A função a seguir está disponível para ser utilizada numa regra de política, mas difere da utilização num modelo de Gestor de Recursos Azure (modelo ARM):

- `utcNow()` - Ao contrário de um modelo ARM, esta propriedade pode ser usada fora _do padrãoValue_.
  - Devolve uma cadeia que está definida para a data e hora atuais no formato Universal ISO 8601 DateTime `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

As seguintes funções só estão disponíveis nas regras políticas:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dataTime**: [Required] string - String in the Universal ISO 8601 DateTime format 'yy-MM-ddTHH:mm:mms. FFFFFZ'
  - **númeroOfDaysToAdd**: [Necessário] inteiro - Número de dias a adicionar
- `field(fieldName)`
  - **nome de campo**: [Obrigatório] cadeia - Nome do [campo](#fields) para recuperar
  - Devolve o valor desse campo do recurso que está a ser avaliado pela condição "Se".
  - `field` é usado principalmente com **AuditIfNotExists** e **DeployIfNotExists** para campos de referência sobre o recurso que estão sendo avaliados. Um exemplo desta utilização pode ser visto no [exemplo do DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Devolve a versão API do pedido que desencadeou a avaliação da política (exemplo: `2019-09-01` ).
    Este valor é a versão API que foi utilizada no pedido PUT/PATCH para avaliações sobre criação/atualização de recursos. A versão mais recente da API é sempre utilizada durante a avaliação de conformidade sobre os recursos existentes.
- `policy()`
  - Devolve as seguintes informações sobre a política que está a ser avaliada. As propriedades podem ser acedidas a partir do objeto devolvido (exemplo: `[policy().assignmentId]` ).
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```


- `ipRangeContains(range, targetRange)`
    - **gama**: [Obrigatório] string - Cadeia especificando uma gama de endereços IP.
    - **targetRange**: [Obrigatório] string - Cadeia especificando uma gama de endereços IP.

    Devolve se o intervalo de endereço IP dado contém o intervalo de endereço IP alvo. Não são permitidas gamas vazias, ou mistura entre famílias ip e resulta em falha de avaliação.

    Formatos suportados:
    - Endereço IP único (exemplos: `10.0.0.0` `2001:0DB8::3:FFFE` )
    - Gama CIDR (exemplos: `10.0.0.0/24` `2001:0DB8::/110` , )
    - Gama definida por endereços IP de início e fim (exemplos: `192.168.0.1-192.168.0.9` `2001:0DB8::-2001:0DB8::3:FFFF` )


#### <a name="policy-function-example"></a>Exemplo de função política

Este exemplo de regra de política usa a `resourceGroup` função de recurso para obter a propriedade do **nome,** combinada com a `concat` função de matriz e objeto para construir uma `like` condição que impõe o nome do recurso para começar com o nome do grupo de recursos.

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

Você usa pseudónimos de propriedade para aceder a propriedades específicas para um tipo de recurso. Os pseudónimos permitem-lhe restringir os valores ou condições permitidos para uma propriedade num recurso. Cada pseudónimo mapeia caminhos em diferentes versões API para um determinado tipo de recurso. Durante a avaliação política, o motor de política obtém o caminho da propriedade para a versão API.

A lista de pseudónimos está sempre a crescer. Para encontrar os pseudónimos atualmente apoiados pela Azure Policy, utilize um dos seguintes métodos:

- Extensão da política Azure para Código do Estúdio Visual (recomendado)

  Utilize a [extensão da Política Azure para o Código do Estúdio Visual](../how-to/extension-for-vscode.md) para visualizar e descobrir pseudónimos para propriedades de recursos.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Screenshot da extensão da Política Azure para Visual Studio Code pairando sobre uma propriedade para exibir os nomes de pseudónimos." border="false":::

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > Para encontrar pseudónimos que possam ser utilizados com o efeito [modificação,](./effects.md#modify) utilize o seguinte comando em Azure PowerShell **4.6.0** ou superior:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- CLI do Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REPOUSO API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Compreender o pseudónimo

Vários dos pseudónimos disponíveis têm uma versão que aparece como um nome 'normal' e outra que **\[\*\]** lhe está associada. Por exemplo:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

O pseudónimo 'normal' representa o campo como um único valor. Este campo é para cenários de comparação de correspondência exatos quando todo o conjunto de valores deve ser exatamente como definido, nem mais nem menos.

O **\[\*\]** pseudónimo representa uma coleção de valores selecionados a partir dos elementos de uma propriedade de recursos de matriz. Por exemplo:

| Alias | Valores selecionados |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | Os elementos da `ipRules` matriz. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | Os valores da `action` propriedade a partir de cada elemento da `ipRules` matriz. |

Quando usados numa condição de [campo,](#fields) os pseudónimos de matriz permite comparar cada elemento de matriz individual com um valor-alvo. Quando usado com expressão [de contagem,](#count) é possível:

- Verifique o tamanho de uma matriz
- Verifique se todos\any\nenhum dos elementos de matriz satisfaz uma condição complexa
- Verifique se exatamente ***n*** elementos de matriz satisfazem uma condição complexa

Para obter mais informações e exemplos, consulte [as propriedades dos recursos da matriz de referência.](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)

## <a name="next-steps"></a>Passos seguintes

- Consulte a estrutura de [definição de iniciativa](./initiative-definition-structure.md)
- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)

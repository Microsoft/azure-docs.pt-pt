---
title: Detalhes da estrutura de definição de política
description: Descreve como a definição de política de recursos é usada pelo Azure Policy para estabelecer convenções para recursos em sua organização, descrevendo quando a política é imposta e qual efeito tomar.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: d415075bda4ff58d4a3a633fe820f22d8a157459
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464025"
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição do Azure Policy

As definições de política de recurso são usadas pelo Azure Policy para estabelecer convenções para recursos. Cada definição descreve a conformidade de recursos e o efeito a ser tomada quando um recurso não está em conformidade.
Ao definir as convenções, você pode controlar os custos e gerenciar seus recursos com mais facilidade. Por exemplo, você pode especificar que apenas determinados tipos de máquinas virtuais são permitidos. Ou, você pode exigir que todos os recursos tenham uma marca específica. As políticas são herdadas por todos os recursos filho. Se uma política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos nesse grupo de recursos.

O esquema de definição de política é encontrado aqui: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Use JSON para criar uma definição de política. A definição de política contém elementos para:

- Moda
- parâmetros
- nome de exibição
- descrição
- Regra de política
  - avaliação lógica
  - funciona

Por exemplo, o JSON a seguir mostra uma política que limita o local em que os recursos são implantados:

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

Todos os exemplos de Azure Policy estão em [exemplos de Azure Policy](../samples/index.md).

## <a name="mode"></a>Modo

O **modo** é configurado dependendo de se a política tem como alvo uma propriedade Azure Resource Manager ou uma propriedade de provedor de recursos.

### <a name="resource-manager-modes"></a>Modos do Resource Manager

O **modo** determina quais tipos de recursos serão avaliados para uma política. Os modos com suporte são:

- `all`: avaliar grupos de recursos e todos os tipos de recursos
- `indexed`: apenas avaliar os tipos de recursos que dão suporte a marcas e local

Recomendamos que você defina o **modo** como `all` na maioria dos casos. Todas as definições de política criadas por meio do portal usam o modo de `all`. Se você usar o PowerShell ou CLI do Azure, poderá especificar o parâmetro de **modo** manualmente. Se a definição de política não incluir um valor de **modo** , o padrão será `all` em Azure PowerShell e `null` no CLI do Azure. Um modo de `null` é o mesmo que usar o `indexed` para dar suporte à compatibilidade com versões anteriores.

`indexed` deve ser usado ao criar políticas que impõem marcas ou locais. Embora não seja necessário, ele impede que os recursos que não dão suporte a marcas e locais sejam mostrados como não compatíveis nos resultados de conformidade. A exceção são os **grupos de recursos**. As políticas que impõem locais ou marcas em um grupo de recursos devem definir o **modo** como `all` e, especificamente, direcionar o tipo de `Microsoft.Resources/subscriptions/resourceGroups`. Para obter um exemplo, consulte [impor marcas de grupo de recursos](../samples/enforce-tag-rg.md). Para obter uma lista de recursos que dão suporte a marcas, consulte [suporte a marcas para recursos do Azure](../../../azure-resource-manager/tag-support.md).

### <a name="a-nameresource-provider-modes-resource-provider-modes-preview"></a><a name="resource-provider-modes" />modos de provedor de recursos (versão prévia)

Atualmente, há suporte para os seguintes modos de provedor de recursos durante a versão prévia:

- `Microsoft.ContainerService.Data` para gerenciar regras do controlador de admissão no [serviço kubernetes do Azure](../../../aks/intro-kubernetes.md). As políticas que usam esse modo de provedor de recursos **devem** usar o efeito [EnforceRegoPolicy](./effects.md#enforceregopolicy) .
- `Microsoft.Kubernetes.Data` para gerenciar clusters kubernetes do mecanismo AKS gerenciados automaticamente no Azure.
  As políticas que usam esse modo de provedor de recursos **devem** usar o efeito [EnforceOPAConstraint](./effects.md#enforceopaconstraint) .
- `Microsoft.KeyVault.Data` para gerenciar cofres e certificados no [Azure Key Vault](../../../key-vault/key-vault-overview.md).

> [!NOTE]
> Os modos de provedor de recursos só dão suporte a definições de políticas internas e não oferecem suporte a iniciativas durante a visualização.

## <a name="parameters"></a>Parâmetros

Os parâmetros ajudam a simplificar o gerenciamento de políticas, reduzindo o número de definições de política. Considere os parâmetros como os campos em um formulário – `name`, `address`, `city``state`. Esses parâmetros sempre permanecem os mesmos, mas seus valores mudam com base no preenchimento individual do formulário.
Os parâmetros funcionam da mesma maneira durante a criação de políticas. Ao incluir parâmetros em uma definição de política, você pode reutilizar essa política para cenários diferentes usando valores diferentes.

> [!NOTE]
> Os parâmetros podem ser adicionados a uma definição existente e atribuída. O novo parâmetro deve incluir a propriedade **DefaultValue** . Isso impede que as atribuições existentes da política ou da iniciativa sejam indiretamente inválidas.

### <a name="parameter-properties"></a>Propriedades do parâmetro

Um parâmetro tem as seguintes propriedades que são usadas na definição de política:

- **nome**: o nome do parâmetro. Usado pela função de implantação `parameters` dentro da regra de política. Para obter mais informações, consulte [usando um valor de parâmetro](#using-a-parameter-value).
- `type`: determina se o parâmetro é uma **cadeia de caracteres**, **matriz**, **objeto**, **booliano**, **inteiro**, **float**ou **DateTime**.
- `metadata`: define as subpropriedades usadas principalmente pelo portal do Azure para exibir informações amigáveis ao usuário:
  - `description`: a explicação de como o parâmetro é usado. Pode ser usado para fornecer exemplos de valores aceitáveis.
  - `displayName`: o nome amigável mostrado no portal para o parâmetro.
  - `strongType`: (opcional) usado ao atribuir a definição de política por meio do Portal. Fornece uma lista de reconhecimento de contexto. Para obter mais informações, consulte [strongtype](#strongtype).
  - `assignPermissions`: (opcional) definido como _true_ para ter portal do Azure criar atribuições de função durante a atribuição de política. Essa propriedade é útil caso você queira atribuir permissões fora do escopo de atribuição. Há uma atribuição de função por definição de função na política (ou por definição de função em todas as políticas na iniciativa). O valor do parâmetro deve ser um recurso ou escopo válido.
- `defaultValue`: (opcional) define o valor do parâmetro em uma atribuição se nenhum valor for fornecido.
  Necessário ao atualizar uma definição de política existente que é atribuída.
- `allowedValues`: (opcional) fornece uma matriz de valores que o parâmetro aceita durante a atribuição.

Por exemplo, você pode definir uma definição de política para limitar os locais em que os recursos podem ser implantados. Um parâmetro para essa definição de política pode ser **allowedLocations**. Esse parâmetro seria usado por cada atribuição da definição de política para limitar os valores aceitos. O uso de **strongtype** fornece uma experiência aprimorada ao concluir a atribuição por meio do portal:

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

Na regra de política, você referencia parâmetros com a seguinte sintaxe de função `parameters`:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Este exemplo faz referência ao parâmetro **allowedLocations** que foi demonstrado nas [Propriedades do parâmetro](#parameter-properties).

### <a name="strongtype"></a>strongType

Na propriedade `metadata`, você pode usar **strongtype** para fornecer uma lista de opções de seleção múltipla dentro do portal do Azure. Os valores permitidos para **strongtype** atualmente incluem:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Local de definição

Ao criar uma iniciativa ou política, é necessário especificar o local de definição. O local de definição deve ser um grupo de gerenciamento ou uma assinatura. Esse local determina o escopo ao qual a iniciativa ou a política pode ser atribuída. Os recursos devem ser membros diretos de ou filhos dentro da hierarquia do local de definição para o destino da atribuição.

Se o local de definição for um:

- Os recursos somente de **assinatura** dentro dessa assinatura podem ser atribuídos à política.
- Recursos somente de **grupo de gerenciamento** dentro de grupos de gerenciamento filho e assinaturas filho podem ser atribuídos à política. Se você planeja aplicar a definição de política a várias assinaturas, o local deve ser um grupo de gerenciamento que contém essas assinaturas.

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Use **DisplayName** e **Description** para identificar a definição de política e fornecer contexto para quando ela for usada. **DisplayName** tem um comprimento máximo de _128_ caracteres e a **Descrição** é de um comprimento máximo de _512_ caracteres.

## <a name="policy-rule"></a>Regra de política

A regra de política consiste em **If** e **then** Blocks. No bloco **If** , você define uma ou mais condições que especificam quando a política é imposta. Você pode aplicar operadores lógicos a essas condições para definir precisamente o cenário de uma política.

No bloco **then** , você define o efeito que ocorre quando as condições **se** são atendidas.

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

Os operadores lógicos com suporte são:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

A sintaxe **not** inverte o resultado da condição. A sintaxe **allOf** (semelhante à operação **and** lógica) requer que todas as condições sejam verdadeiras. A sintaxe **anyOf** (semelhante à operação **or** lógica) requer que uma ou mais condições sejam verdadeiras.

Você pode aninhar operadores lógicos. O exemplo a seguir mostra uma operação **not** que está aninhada em uma operação **allOf** .

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

Uma condição avalia se um **campo** ou o acessador de **valor** atende a determinados critérios. As condições com suporte são:

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
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Ao usar as condições **like** e não **like** , você fornece um curinga `*` no valor.
O valor não deve ter mais de um curinga `*`.

Ao usar as condições **Match** e não **match** , forneça `#` para corresponder a um dígito, `?` para uma letra, `.` para corresponder a qualquer caractere e qualquer outro caractere para corresponder a esse caractere real.
**Match** e não **Match** diferenciam maiúsculas de minúsculas. As alternativas que não diferenciam maiúsculas de minúsculas estão disponíveis em **matchInsensitively** e **notMatchInsensitively**. Para obter exemplos, consulte [permitir vários padrões de nome](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Campos

As condições são formadas usando campos. Um campo corresponde às propriedades na carga de solicitação do recurso e descreve o estado do recurso.

Os campos a seguir têm suporte:

- `name`
- `fullName`
  - Retorna o nome completo do recurso. O nome completo de um recurso é o nome do recurso que foi anexado por qualquer nome de recurso pai (por exemplo, "meuservidor/MyDatabase").
- `kind`
- `type`
- `location`
  - Use **global** para recursos que são independentes de local. Para obter um exemplo, consulte [exemplos de locais permitidos](../samples/allowed-locations.md).
- `identity.type`
  - Retorna o tipo de [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) habilitada no recurso.
- `tags`
- `tags['<tagName>']`
  - Essa sintaxe de colchete dá suporte a nomes de marca que têm pontuação, como um hífen, um ponto final ou um espaço.
  - Onde **\<tagName\>** é o nome da marca para validar a condição.
  - Exemplos: `tags['Acct.CostCenter']` em que **Acct. CostCenter** é o nome da marca.
- `tags['''<tagName>''']`
  - Essa sintaxe de colchetes dá suporte a nomes de marca que têm apóstrofos na saída, com apóstrofos duplos.
  - Em que **'\<tagName\>'** é o nome da marca para validar a condição.
  - Exemplo: `tags['''My.Apostrophe.Tag''']` onde **' My. apóstrofo. tag '** é o nome da marca.
- aliases de propriedade-para obter uma lista, consulte [aliases](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`e `tags[tag.with.dots]` ainda são formas aceitáveis de declarar um campo de marcas. No entanto, as expressões preferenciais são as listadas acima.

#### <a name="use-tags-with-parameters"></a>Usar marcas com parâmetros

Um valor de parâmetro pode ser passado para um campo de marca. A passagem de um parâmetro para um campo de marca aumenta a flexibilidade da definição de política durante a atribuição de política.

No exemplo a seguir, `concat` é usado para criar uma pesquisa de campo de marcas para a marca chamada o valor do parâmetro **TagName** . Se essa marca não existir, o efeito **Modificar** será usado para adicionar a marca usando o valor da mesma marca nomeada definida no grupo de recursos pai de recursos auditados usando a função de pesquisa `resourcegroup()`.

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

As condições também podem ser formadas usando o **valor**. o **valor** verifica as condições em relação a [parâmetros](#parameters), [funções de modelo com suporte](#policy-functions)ou literais.
o **valor** é emparelhado com qualquer [condição](#conditions)com suporte.

> [!WARNING]
> Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falhará. Uma avaliação com falha é uma **negação**implícita. Para obter mais informações, consulte [evitando falhas de modelo](#avoiding-template-failures).

#### <a name="value-examples"></a>Exemplos de valor

Este exemplo de regra de política usa o **valor** para comparar o resultado da função `resourceGroup()` e a propriedade **Name** retornada como uma condição **like** de `*netrg`. A regra nega qualquer recurso que não seja do **tipo** de `Microsoft.Network/*` em qualquer grupo de recursos cujo nome termine em `*netrg`.

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

Este exemplo de regra de política usa **valor** para verificar se o resultado de várias funções aninhadas **é igual** a `true`. A regra nega qualquer recurso que não tenha pelo menos três marcas.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Evitando falhas de modelo

O uso de _funções de modelo_ em **valor** permite muitas funções aninhadas complexas. Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falhará. Uma avaliação com falha é uma **negação**implícita. Um exemplo de um **valor** que falha em determinados cenários:

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

A regra de política de exemplo acima usa [substring ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) para comparar os três primeiros caracteres de **nome** para **ABC**. Se **Name** for menor que três caracteres, a função `substring()` resultará em um erro. Esse erro faz com que a política se torne um efeito de **negação** .

Em vez disso, use a função [If ()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) para verificar se os três primeiros caracteres de **nome** são iguais a **ABC** sem permitir que um **nome** com menos de três caracteres cause um erro:

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

Com a regra de política revisada, `if()` verifica o comprimento do **nome** antes de tentar obter uma `substring()` em um valor com menos de três caracteres. Se o **nome** for muito curto, o valor "não iniciando com ABC" será retornado em vez disso e comparado com **ABC**. Um recurso com um nome curto que não começa com **ABC** ainda falha na regra de política, mas não causa mais um erro durante a avaliação.

### <a name="effect"></a>Efeito

O Azure Policy dá suporte aos seguintes tipos de efeito:

- **Append**: Adiciona o conjunto definido de campos à solicitação
- **Auditoria**: gera um evento de aviso no log de atividades, mas não falha na solicitação
- **AuditIfNotExists**: gera um evento de aviso no log de atividades se um recurso relacionado não existir
- **Deny**: gera um evento no log de atividades e falha na solicitação
- **DeployIfNotExists**: implanta um recurso relacionado se ele ainda não existir
- **Desabilitado**: não avalia os recursos de conformidade com a regra de política
- **EnforceOPAConstraint** (visualização): configura o controlador de admissão do agente de política aberto com o gatekeeper V3 para clusters kubernetes autogerenciados no Azure (versão prévia)
- **EnforceRegoPolicy** (versão prévia): configura o controlador de admissão do agente de política aberto com o gatekeeper V2 no serviço kubernetes do Azure
- **Modificar**: adiciona, atualiza ou remove as marcas definidas de um recurso

Para obter detalhes completos sobre cada efeito, ordem de avaliação, propriedades e exemplos, consulte [noções básicas sobre efeitos de Azure Policy](effects.md).

### <a name="policy-functions"></a>Funções de política

Todas as [funções de modelo do Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) estão disponíveis para uso em uma regra de política, exceto as funções e funções definidas pelo usuário a seguir:

- copyIndex ()
- implantação ()
- lista
- newGuid ()
- pickZones()
- provedores ()
- referência ()
- ResourceId ()
- variáveis ()

As funções a seguir estão disponíveis para uso em uma regra de política, mas diferem do uso em um modelo de Azure Resource Manager:

- AddDays (dateTime, numberOfDaysToAdd)
  - **DateTime**: [Required] String-String no formato de data/hora Universal ISO 8601 ' yyyy-mm-ddThh: mm: SS. fffffffZ '
  - **numberOfDaysToAdd**: [obrigatório] número inteiro de dias para adicionar
- utcNow () – ao contrário de um modelo do Resource Manager, ele pode ser usado fora de defaultValue.
  - Retorna uma cadeia de caracteres que é definida como a data e a hora atuais no formato universal ISO 8601 DateTime ' YYYY-MM-ddTHH: mm: SS. fffffffZ '

Além disso, a função `field` está disponível para regras de política. `field` é usada principalmente com **AuditIfNotExists** e **DeployIfNotExists** para fazer referência a campos no recurso que estão sendo avaliados. Um exemplo desse uso pode ser visto no [exemplo DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Exemplo de função de política

Este exemplo de regra de política usa a função de recurso `resourceGroup` para obter a propriedade **Name** , combinada com a `concat` matriz e a função de objeto para criar uma condição de `like` que impõe o nome do recurso para começar com o nome do grupo de recursos.

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

Você usa aliases de propriedade para acessar propriedades específicas de um tipo de recurso. Os aliases permitem restringir quais valores ou condições são permitidos para uma propriedade em um recurso. Cada alias é mapeado para caminhos em versões de API diferentes para um determinado tipo de recurso. Durante a avaliação da política, o mecanismo de política Obtém o caminho da propriedade para essa versão de API.

A lista de aliases está sempre crescendo. Para saber quais aliases têm suporte no momento por Azure Policy, use um dos seguintes métodos:

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

- API REST/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Compreendendo o alias [*]

Vários dos aliases que estão disponíveis têm uma versão que aparece como um nome ' normal ' e outro que tem **[\*]** anexado a ele. Por exemplo:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

O alias ' normal ' representa o campo como um único valor. Esse campo é para cenários de comparação de correspondência exata quando o conjunto inteiro de valores deve ser exatamente o mesmo definido, nem mais nem menos.

O alias **[\*]** torna possível comparar com o valor de cada elemento na matriz e propriedades específicas de cada elemento. Essa abordagem possibilita comparar as propriedades do elemento para ' If None of ', ' if any of ', ou ' If All of '. Usando **ipRules [\*]** , um exemplo seria validar se cada _ação_ é _Deny_, mas não se preocupa com quantas regras existem ou qual é o _valor_ de IP. Esta regra de exemplo verifica se há correspondências de **ipRules [\*]. Value** para **10.0.4.1** e aplica o **effecttype** somente se ele não encontrar pelo menos uma correspondência:

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

Para obter mais informações, consulte [avaliando o alias [\*]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Iniciativas

As iniciativas permitem agrupar várias definições de políticas relacionadas para simplificar as atribuições e o gerenciamento, pois você trabalha com um grupo como um único item. Por exemplo, você pode agrupar definições de política de marcação relacionadas em uma única iniciativa. Em vez de atribuir cada política individualmente, você aplica a iniciativa.

O exemplo a seguir ilustra como criar uma iniciativa para lidar com duas marcas: `costCenter` e `productName`. Ele usa duas políticas internas para aplicar o valor de marca padrão.

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
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
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
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).

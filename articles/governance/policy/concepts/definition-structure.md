---
title: Detalhes da estrutura de definição de política
description: Descreve como a definição de política de recurso do Azure Policy é utilizada para estabelecer as convenções para recursos na sua organização com a descrição quando a política é imposta e o efeito resultante para tirar.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 27cf1539fc98b2ad7f1b82e194989c1619ab99fb
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980713"
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição do Azure Policy

Definições de política de recursos são utilizadas pelo Azure Policy para estabelecer as convenções de recursos. Cada definição descreve o que afetar a tomar quando um recurso está em conformidade e de conformidade de recursos.
Ao definir as convenções, pode controlar os custos e gerir mais facilmente os seus recursos. Por exemplo, pode especificar que apenas determinados tipos de máquinas virtuais são permitidos. Em alternativa, pode exigir que todos os recursos tenham uma etiqueta específica. As políticas são herdadas por todos os recursos subordinados. Se uma política é aplicada a um grupo de recursos, é aplicável a todos os recursos nesse grupo de recursos.

O esquema de definição de política é encontrado aqui: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Utilizar o JSON para criar uma definição de política. A definição de política contém elementos para:

- mode
- parâmetros
- Nome a apresentar
- descrição
- regra de política
  - avaliação de lógica
  - em vigor

Por exemplo, o JSON seguinte mostra uma política que limita a onde os recursos são implementados:

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

O **modo** determina quais tipos de recursos serão avaliados para uma política. Os modos suportados são:

- `all`: avaliar a grupos de recursos e todos os tipos de recursos
- `indexed`: avaliar apenas tipos de recursos que oferecem suporte a marcas e localização

Recomendamos que defina **modo** para `all` na maioria dos casos. Todas as definições de política criadas através da utilização de portal a `all` modo. Se utilizar o PowerShell ou da CLI do Azure, pode especificar a **modo** parâmetro manualmente. Se a definição de política não inclui um **modo** valor, assume como predefinição `all` no Azure PowerShell e, a `null` na CLI do Azure. R `null` modo de é igual a utilizar `indexed` para suportar a compatibilidade com versões anteriores.

`indexed` deve ser usado durante a criação de políticas que aplicar etiquetas ou localizações. Embora não seja necessário, ele impede que os recursos que não dão suporte a marcas e locais sejam mostrados como não compatíveis nos resultados de conformidade. A exceção é **grupos de recursos**. Devem definir políticas que impõem a localização ou etiquetas num grupo de recursos **modo** ao `all` e o destino especificamente o `Microsoft.Resources/subscriptions/resourceGroups` tipo. Por exemplo, veja [impor etiquetas do grupo de recursos](../samples/enforce-tag-rg.md). Para obter uma lista de recursos que dão suporte a marcas, consulte [suporte a marcas para recursos do Azure](../../../azure-resource-manager/tag-support.md).

### <a name="resource-provider-modes"></a>Modos de provedor de recursos

O único modo de provedor de recursos com suporte no momento é `Microsoft.ContainerService.Data` para gerenciar regras do controlador de admissão no [serviço kubernetes do Azure](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy para kubernetes](rego-for-aks.md) está em visualização pública e só dá suporte a definições de políticas internas.

## <a name="parameters"></a>Parâmetros

Parâmetros ajudam a simplificar a gestão de políticas ao reduzir o número de definições de política. Pense em parâmetros como os campos num formulário – `name`, `address`, `city`, `state`. Esses parâmetros são sempre os mesmos, no entanto alterar seus valores com base em individuais a preencher o formulário.
Parâmetros funcionam da mesma forma, na criação de políticas. Ao incluir parâmetros na definição de política, pode reutilizar essa política para diferentes cenários com valores diferentes.

> [!NOTE]
> Os parâmetros podem ser adicionados a uma definição existente e atribuída. O novo parâmetro deve incluir a propriedade **DefaultValue** . Isto impede que existente atribuições da política ou iniciativa indiretamente que estão sendo feitas inválido.

### <a name="parameter-properties"></a>Propriedades do parâmetro

Um parâmetro tem as seguintes propriedades que são usadas na definição de política:

- **nome**: O nome do parâmetro. Usado pela função de implantação `parameters` dentro da regra de política. Para obter mais informações, consulte [usando um valor de parâmetro](#using-a-parameter-value).
- `type`: Determina se o parâmetro é uma **cadeia de caracteres**, **matriz**, **objeto**, **booliano**, **inteiro**, **float**ou **DateTime**.
- `metadata`: Define as subpropriedades usadas principalmente pelo portal do Azure para exibir informações amigáveis ao usuário:
  - `description`: A explicação sobre o que o parâmetro é usado para. Pode ser usado para fornecer exemplos de valores aceitáveis.
  - `displayName`: O nome amigável mostrado no portal para o parâmetro.
  - `strongType`: Adicional Usado ao atribuir a definição de política por meio do Portal. Fornece uma lista de reconhecimento de contexto. Para obter mais informações, consulte [strongtype](#strongtype).
  - `assignPermissions`: Adicional Defina como _true_ para que Portal do Azure crie atribuições de função durante a atribuição de política. Essa propriedade é útil caso você queira atribuir permissões fora do escopo de atribuição. Há uma atribuição de função por definição de função na política (ou por definição de função em todas as políticas na iniciativa). O valor do parâmetro deve ser um recurso ou escopo válido.
- `defaultValue`: Adicional Define o valor do parâmetro em uma atribuição se nenhum valor for fornecido.
  Necessário ao atualizar uma definição de política existente que é atribuída.
- `allowedValues`: Adicional Fornece uma matriz de valores que o parâmetro aceita durante a atribuição.

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

A regra de política, fazer referência a parâmetros com o seguinte `parameters` sintaxe de função de valor de implementação:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Este exemplo faz referência ao parâmetro **allowedLocations** que foi demonstrado nas [Propriedades do parâmetro](#parameter-properties).

### <a name="strongtype"></a>strongType

Na propriedade `metadata`, você pode usar **strongtype** para fornecer uma lista de opções de seleção múltipla dentro do portal do Azure. Valores para permitidos **strongType** atualmente incluem:

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

## <a name="definition-location"></a>Localização da definição

Ao criar uma política ou iniciativa, é necessário especificar a localização da definição. A localização da definição tem de ser um grupo de gestão ou de uma subscrição. Esta localização determina o âmbito para o qual pode ser atribuída a política ou iniciativa. Recursos tem de ser membros diretos dos ou elementos subordinados na hierarquia da localização da definição de destino para a atribuição.

Se a localização da definição é r:

- **Subscrição** – apenas recursos nessa subscrição podem ser atribuídos a política.
- **Grupo de gestão** – apenas recursos dentro de grupos de gestão de subordinados e subscrições de subordinados podem ser atribuídos a política. Se planeia aplicar a definição de política para várias subscrições, a localização tem de ser um grupo de gestão que contém nessas subscrições.

## <a name="display-name-and-description"></a>Nome a apresentar e descrição

Utilizar **displayName** e **Descrição** para identificar a definição de política e fornecer contexto para quando é utilizado. **DisplayName** tem um comprimento máximo de _128_ caracteres e a **Descrição** é de um comprimento máximo de _512_ caracteres.

## <a name="policy-rule"></a>regra de política

A regra de política é composta por **se** e **, em seguida,** blocos. Na **se** bloco, define uma ou mais condições que especificar quando a política é imposta. Pode aplicar operadores lógicos para estas condições para definir exatamente o cenário para uma política.

Na **, em seguida,** bloco, define o efeito que acontece quando o **se** condições são cumpridas.

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

Operadores lógicos suportados são:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

O **não** sintaxe inverts o resultado da condição. O **tudo** sintaxe (semelhante ao lógico **e** operação) requer que todas as condições como true. O **anyOf** sintaxe (semelhante ao lógico **ou** operação) requer um ou mais condições como true.

Pode aninhar operadores lógicos. A exemplo a seguir mostra um **não** operação que esteja aninhada dentro de um **tudo** operação.

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

Uma condição avalia se um **campo** ou o acessador de **valor** atende a determinados critérios. As condições suportadas são:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Ao utilizar o **, como** e **notLike** condições, que fornece um caráter universal `*` no valor.
O valor não deve ter mais de um caráter universal `*`.

Ao usar as condições **Match** e não **match** , forneça `#` para corresponder a um dígito, `?` para uma letra, `.` para corresponder a qualquer caractere e qualquer outro caractere para corresponder a esse caractere real.
**Match** e não **Match** diferenciam maiúsculas de minúsculas. As alternativas que não diferenciam maiúsculas de minúsculas estão disponíveis em **matchInsensitively** e **notMatchInsensitively**. Para obter exemplos, consulte [permitir que vários padrões de nome](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Campos

Condições são formadas com campos. Um campo corresponde a propriedades no payload de pedido de recurso e descreve o estado do recurso.

São suportados os seguintes campos:

- `name`
- `fullName`
  - Devolve o nome completo do recurso. O nome completo de um recurso é o nome de recurso anexado por nenhum nome de recurso principal (por exemplo "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Use **global** para recursos que são independentes de local. Para obter um exemplo, consulte [exemplos de locais permitidos](../samples/allowed-locations.md).
- `identity.type`
  - Retorna o tipo de [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) habilitada no recurso.
- `tags`
- `tags['<tagName>']`
  - Essa sintaxe de colchete dá suporte a nomes de marca que têm pontuação, como um hífen, um ponto final ou um espaço.
  - Em que **\<tagName\>** é o nome da etiqueta para validar a condição para.
  - Exemplos: `tags['Acct.CostCenter']` em que **Acct. CostCenter** é o nome da marca.
- `tags['''<tagName>''']`
  - Essa sintaxe de colchetes dá suporte a nomes de marca que têm apóstrofos na saída, com apóstrofos duplos.
  - Em que **' \<tagName @ no__t-2 '** é o nome da marca para validar a condição.
  - Exemplo: `tags['''My.Apostrophe.Tag''']`, em que **' \<tagName @ no__t-3 '** é o nome da marca.
- aliases de propriedade - para obter uma lista, consulte [Aliases](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` e `tags[tag.with.dots]` ainda são maneiras aceitáveis de declarar um campo de marcas. No entanto, as expressões preferenciais são as listadas acima.

#### <a name="use-tags-with-parameters"></a>Usar marcas com parâmetros

Um valor de parâmetro pode ser passado para um campo de marca. A passagem de um parâmetro para um campo de marca aumenta a flexibilidade da definição de política durante a atribuição de política.

No exemplo a seguir, `concat` é usado para criar uma pesquisa de campo de marcas para a marca denominada o valor do parâmetro **TagName** . Se essa marca não existir, o efeito de **acréscimo** será usado para adicionar a marca usando o valor da mesma marca nomeada definida no grupo de recursos pai de recursos auditados usando a função de pesquisa `resourcegroup()`.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Value

As condições também podem ser formadas usando o **valor**. o **valor** verifica as condições em relação a [parâmetros](#parameters), [funções de modelo com suporte](#policy-functions)ou literais.
o **valor** é emparelhado com qualquer [condição](#conditions)com suporte.

> [!WARNING]
> Se o resultado de uma _função de modelo_ for um erro, a avaliação da política falhará. Uma avaliação com falha é uma **negação**implícita. Para obter mais informações, consulte [evitando falhas de modelo](#avoiding-template-failures).

#### <a name="value-examples"></a>Exemplos de valor

Este exemplo de regra de política usa o **valor** para comparar o resultado da função `resourceGroup()` e a propriedade **nome** retornada como uma condição **like** de `*netrg`. A regra nega qualquer recurso que não seja do **tipo** `Microsoft.Network/*` em qualquer grupo de recursos cujo nome termine em `*netrg`.

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

Com a regra de política revisada, `if()` verifica o comprimento do **nome** antes de tentar obter um `substring()` em um valor com menos de três caracteres. Se o **nome** for muito curto, o valor "não iniciando com ABC" será retornado em vez disso e comparado com **ABC**. Um recurso com um nome curto que não começa com **ABC** ainda falha na regra de política, mas não causa mais um erro durante a avaliação.

### <a name="effect"></a>Efeito

O Azure Policy dá suporte aos seguintes tipos de efeito:

- **Negar**: gera um evento no registo de atividades e falha do pedido
- **Auditoria**: gera um evento de aviso no registo de atividades, mas não falhar o pedido
- **Acrescentar**: Adiciona o conjunto definido de campos ao pedido
- **AuditIfNotExists**: permite a auditoria se não existir um recurso
- **DeployIfNotExists**: implementa um recurso, se ainda não exista
- **Desativado**: não avaliar os recursos de conformidade para a regra de política
- **EnforceRegoPolicy**: configura o controlador de admissão do agente de política aberto no serviço kubernetes do Azure (versão prévia)
- **Modificar**: adiciona, atualiza ou remove as marcas definidas de um recurso

Para **acrescentar**, tem de fornecer os seguintes detalhes:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

O valor pode ser uma cadeia de caracteres ou um objeto de formato JSON.

**AuditIfNotExists** e **DeployIfNotExists** avaliar a existência de um recurso relacionado e aplicar uma regra. Se o recurso não corresponder a regra, o efeito é implementado. Por exemplo, pode exigir que um observador de rede é implementado para todas as redes virtuais. Para obter mais informações, consulte a [auditar se a extensão não existe](../samples/audit-ext-not-exist.md) exemplo.

O **DeployIfNotExists** efeito requer a **roleDefinitionId** propriedade no **detalhes** parte da regra de política. Para obter mais informações, consulte [remediação - configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Da mesma forma, **Modify** requer a propriedade **roleDefinitionId** na parte de **detalhes** da regra de política para a [tarefa de correção](../how-to/remediate-resources.md). **Modify** também requer uma matriz de **operações** para definir as ações a serem executadas nas marcas de recursos.

Para obter detalhes completos sobre cada efeito, ordem de avaliação, propriedades e exemplos, consulte [noções básicas sobre efeitos de Azure Policy](effects.md).

### <a name="policy-functions"></a>Funções de política

Todas as [funções de modelo do Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) estão disponíveis para uso em uma regra de política, exceto as funções e funções definidas pelo usuário a seguir:

- copyIndex()
- deployment()
- list*
- newGuid ()
- pickZones()
- provedores ()
- reference()
- resourceId()
- variáveis ()

As funções a seguir estão disponíveis para uso em uma regra de política, mas diferem do uso em um modelo de Azure Resource Manager:

- AddDays (dateTime, numberOfDaysToAdd)
  - **DateTime**: [Required] String-String no formato de data/hora Universal ISO 8601 ' yyyy-mm-ddThh: mm: SS. fffffffZ '
  - **numberOfDaysToAdd**: [obrigatório] número inteiro de dias para adicionar
- utcNow () – ao contrário de um modelo do Resource Manager, ele pode ser usado fora de defaultValue.
  - Retorna uma cadeia de caracteres que é definida como a data e a hora atuais no formato universal ISO 8601 DateTime ' YYYY-MM-ddTHH: mm: SS. fffffffZ '

Além disso, o `field` função está disponível para as regras de política. `field` é utilizado principalmente com **AuditIfNotExists** e **DeployIfNotExists** aos campos de referência no recurso que estão a ser avaliados. Um exemplo desta utilização pode ser visto na [DeployIfNotExists exemplo](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Exemplo de função de política

Este exemplo de regra de política utiliza o `resourceGroup` função de recursos para obter o **nome** propriedade, combinada com o `concat` matriz e objeto de função para criar um `like` condição que impõe o nome do recurso para começar com o nome do grupo de recursos.

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

Utilize aliases de propriedade para aceder às propriedades específicas para um tipo de recurso. Aliases permitem-lhe restringir quais valores ou condições são permitidas para uma propriedade num recurso. Cada alias mapeia para caminhos em diferentes versões de API para um tipo de recurso específico. Durante a avaliação da política, o mecanismo da diretiva obtém o caminho de propriedade para essa versão de API.

A lista de aliases está sempre a aumentar. Para localizar os aliases são atualmente suportadas pelo Azure Policy, utilize um dos seguintes métodos:

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

### <a name="understanding-the--alias"></a>Noções básicas sobre o alias [*]

Vários dos aliases que estão disponíveis possuem uma versão que é apresentado como um nome de "normal" e outro que tenha **[\*]** ligados ao mesmo. Por exemplo:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

O alias ' normal ' representa o campo como um único valor. Esse campo é para cenários de comparação de correspondência exata quando o conjunto inteiro de valores deve ser exatamente o mesmo definido, nem mais nem menos.

O alias **[\*]** torna possível comparar com o valor de cada elemento na matriz e propriedades específicas de cada elemento. Essa abordagem possibilita comparar as propriedades do elemento para ' If None of ', ' if any of ', ou ' If All of '. Usando **ipRules [\*]** , um exemplo seria validar se cada _ação_ é _Deny_, mas não se preocupa com quantas regras existem ou qual é o _valor_ de IP. Esta regra de exemplo verifica se há correspondências de **ipRules [\*]. valor** para **10.0.4.1** e aplica o **effecttype** somente se ele não encontrar pelo menos uma correspondência:

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

Iniciativas permitem-lhe agrupar várias definições de política relacionada para simplificar a gestão e as atribuições de como trabalhar com um grupo como um único item. Por exemplo, pode agrupar as definições de política de etiquetagem relacionados numa única iniciativa. Em vez de atribuir cada política individualmente, aplicar a iniciativa.

O exemplo a seguir ilustra como criar uma iniciativa para lidar com duas etiquetas: `costCenter` e `productName`. Ele usa duas políticas incorporadas para aplicar o valor da etiqueta predefinida.

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
---
title: Detalhes da estrutura de definição de política
description: Descreve como a definição de política de recurso do Azure Policy é utilizada para estabelecer as convenções para recursos na sua organização com a descrição quando a política é imposta e o efeito resultante para tirar.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 35cb5c286b9c9657c37dcede7f51082b5c48ef99
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894432"
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição do Azure Policy

Definições de política de recursos são utilizadas pelo Azure Policy para estabelecer as convenções de recursos. Cada definição descreve o que afetar a tomar quando um recurso está em conformidade e de conformidade de recursos.
Ao definir as convenções, pode controlar os custos e gerir mais facilmente os seus recursos. Por exemplo, pode especificar que apenas determinados tipos de máquinas virtuais são permitidos. Em alternativa, pode exigir que todos os recursos tenham uma etiqueta específica. As políticas são herdadas por todos os recursos subordinados. Se uma política é aplicada a um grupo de recursos, é aplicável a todos os recursos nesse grupo de recursos.

O esquema utilizado pela política do Azure pode ser encontrado aqui: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

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
                "defaultValue": "westus2"
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

Todos os exemplos do Azure Policy correm [exemplos de política](../samples/index.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Modo

O **modo** determina quais tipos de recursos serão avaliados para uma política. Os modos suportados são:

- `all`: avaliar a grupos de recursos e todos os tipos de recursos
- `indexed`: avaliar apenas tipos de recursos que oferecem suporte a marcas e localização

Recomendamos que defina **modo** para `all` na maioria dos casos. Todas as definições de política criadas através da utilização de portal a `all` modo. Se utilizar o PowerShell ou da CLI do Azure, pode especificar a **modo** parâmetro manualmente. Se a definição de política não inclui um **modo** valor, assume como predefinição `all` no Azure PowerShell e, a `null` na CLI do Azure. R `null` modo de é igual a utilizar `indexed` para suportar a compatibilidade com versões anteriores.

`indexed` deve ser usado durante a criação de políticas que aplicar etiquetas ou localizações. Embora não seja necessário, impede que os recursos que não suportam etiquetas e localizações de aparecer como não conformes nos resultados de compatibilidade. A exceção é **grupos de recursos**. Devem definir políticas que impõem a localização ou etiquetas num grupo de recursos **modo** ao `all` e o destino especificamente o `Microsoft.Resources/subscriptions/resourceGroups` tipo. Por exemplo, veja [impor etiquetas do grupo de recursos](../samples/enforce-tag-rg.md). Para obter uma lista de recursos que suportam etiquetas, consulte [marca o suporte para recursos do Azure](../../../azure-resource-manager/tag-support.md).

## <a name="parameters"></a>Parâmetros

Parâmetros ajudam a simplificar a gestão de políticas ao reduzir o número de definições de política. Pense em parâmetros como os campos num formulário – `name`, `address`, `city`, `state`. Esses parâmetros são sempre os mesmos, no entanto alterar seus valores com base em individuais a preencher o formulário.
Parâmetros funcionam da mesma forma, na criação de políticas. Ao incluir parâmetros na definição de política, pode reutilizar essa política para diferentes cenários com valores diferentes.

> [!NOTE]
> Parâmetros podem ser adicionados a uma definição existente e atribuída. O novo parâmetro tem de incluir o **defaultValue** propriedade. Isto impede que existente atribuições da política ou iniciativa indiretamente que estão sendo feitas inválido.

### <a name="parameter-properties"></a>Propriedades do parâmetro

Um parâmetro tem as seguintes propriedades que são utilizadas na definição de política:

- **name**: O nome do seu parâmetro. Utilizado pelo `parameters` função de implementação dentro da regra de política. Para obter mais informações, consulte [usando um valor de parâmetro](#using-a-parameter-value).
- `type`: Determina se o parâmetro é um **cadeia de caracteres** ou uma **matriz**.
- `metadata`: Define subproperties principalmente utilizados pelo portal do Azure para apresentar informações amigáveis:
  - `description`: A explicação sobre o que o parâmetro é utilizado para. Pode ser utilizado para fornecer exemplos de valores aceitáveis.
  - `displayName`: O nome amigável apresentado no portal para o parâmetro.
  - `strongType`: (Opcional) Utilizadas quando atribui a definição de política através do portal. Fornece uma lista com reconhecimento de contexto. Para obter mais informações, consulte [strongType](#strongtype).
- `defaultValue`: (Opcional) Define o valor do parâmetro de uma atribuição, se nenhum valor é atribuído. É necessário quando atualizar uma definição de política existente que está atribuída.
- `allowedValues`: (Opcional) Fornece uma matriz de valores que o parâmetro aceita durante a atribuição.

Por exemplo, pode definir uma definição de política para limitar as localizações onde os recursos podem ser implementados. Um parâmetro para essa definição de política pode ser **allowedLocations**. Este parâmetro seria usado por cada atribuição da definição de política para limitar os valores aceites. A utilização de **strongType** fornece uma experiência aprimorada ao concluir a atribuição através do portal:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "westus2",
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

Este exemplo faz referência a **allowedLocations** parâmetro que foi demonstrado [propriedades do parâmetro](#parameter-properties).

### <a name="strongtype"></a>strongType

Dentro de `metadata` propriedade, pode usar **strongType** para fornecer uma lista de seleção múltipla de opções no portal do Azure. Valores para permitidos **strongType** atualmente incluem:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

## <a name="definition-location"></a>Localização da definição

Ao criar uma política ou iniciativa, é necessário especificar a localização da definição. A localização da definição tem de ser um grupo de gestão ou de uma subscrição. Esta localização determina o âmbito para o qual pode ser atribuída a política ou iniciativa. Recursos tem de ser membros diretos dos ou elementos subordinados na hierarquia da localização da definição de destino para a atribuição.

Se a localização da definição é r:

- **Subscrição** – apenas recursos nessa subscrição podem ser atribuídos a política.
- **Grupo de gestão** – apenas recursos dentro de grupos de gestão de subordinados e subscrições de subordinados podem ser atribuídos a política. Se planeia aplicar a definição de política para várias subscrições, a localização tem de ser um grupo de gestão que contém nessas subscrições.

## <a name="display-name-and-description"></a>Nome a apresentar e descrição

Utilizar **displayName** e **Descrição** para identificar a definição de política e fornecer contexto para quando é utilizado. **displayName** tem um comprimento máximo de _128_ carateres e **Descrição** um comprimento máximo de _512_ carateres.

## <a name="policy-rule"></a>Regra de política

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

A condição for avaliada se um **campo** ou o **valor** acessador atende a certos critérios. As condições suportadas são:

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
- `"exists": "bool"`

Ao utilizar o **, como** e **notLike** condições, que fornece um caráter universal `*` no valor.
O valor não deve ter mais de um caráter universal `*`.

Ao utilizar o **corresponder** e **notMatch** condições, fornecer `#` de acordo com um dígito, `?` para uma letra, `.` de acordo com todos os caracteres e qualquer outro caractere para corresponder ao esse caractere real.
**corresponder** e **notMatch** diferenciam maiúsculas de minúsculas. Alternativas de maiúsculas e minúsculas estão disponíveis no **matchInsensitively** e **notMatchInsensitively**. Para obter exemplos, consulte [permitir que vários padrões de nome](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Campos

Condições são formadas com campos. Um campo corresponde a propriedades no payload de pedido de recurso e descreve o estado do recurso.

São suportados os seguintes campos:

- `name`
- `fullName`
  - Devolve o nome completo do recurso. O nome completo de um recurso é o nome de recurso anexado por nenhum nome de recurso principal (por exemplo "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Uso **global** para recursos que desconhecem de localização. Por exemplo, veja [exemplos - localizações permitidos](../samples/allowed-locations.md).
- `identity.type`
  - Devolve o tipo de [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md) ativada no recurso.
- `tags`
- `tags['<tagName>']`
  - Essa sintaxe de colchete suporta nomes de etiqueta com a pontuação, como um hífen, ponto ou espaço.
  - Em que **\<tagName\>** é o nome da etiqueta para validar a condição para.
  - Exemplos: `tags['Acct.CostCenter']` em que **Acct.CostCenter** é o nome da etiqueta.
- `tags['''<tagName>''']`
  - Essa sintaxe de colchete suporta nomes de etiquetas que tenham apóstrofos ao Escapadelas com apóstrofos duplos.
  - Em que **'\<tagName\>'** é o nome da etiqueta para validar a condição para.
  - Exemplo: `tags['''My.Apostrophe.Tag''']` em que **'\<tagName\>'** é o nome da etiqueta.
- aliases de propriedade - para obter uma lista, consulte [Aliases](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`, e `tags[tag.with.dots]` ainda aceitável formas de declaração de um campo de etiquetas.
> No entanto, as expressões preferenciais são aqueles listados acima.

#### <a name="use-tags-with-parameters"></a>Utilizar etiquetas com parâmetros

Um valor de parâmetro pode ser passado para um campo de etiqueta. Passar um parâmetro para um campo de etiqueta aumenta a flexibilidade de definição de política durante a atribuição de política.

No exemplo a seguir `concat` é utilizado para criar uma pesquisa de campo de etiquetas para a marca com o nome do valor da **tagName** parâmetro. Se essa marca não existir, o **acrescentar** efeito é usado para adicionar a marca usando o valor da mesma etiqueta nomeado, definir sobre o grupo de recursos do principal de recursos auditada com o `resourcegroup()` função de pesquisa.

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

Também podem ser formadas condições usando **valor**. **valor** verifica condições contra [parâmetros](#parameters), [suportada de funções de modelo](#policy-functions), ou literais.
**valor** é emparelhado com qualquer suportado [condição](#conditions).

> [!WARNING]
> Se o resultado de uma _função de modelo_ é um erro, a falha de avaliação da política. Uma avaliação com falhas é uma implícita **negar**. Para obter mais informações, consulte [evitar falhas de modelo](#avoiding-template-failures).

#### <a name="value-examples"></a>Exemplos de valor

Este exemplo de regra de política utiliza **valor** comparar o resultado do `resourceGroup()` função e retornado **nome** propriedade para um **como** condição de `*netrg`. A regra nega qualquer recurso não dos `Microsoft.Network/*` **tipo** em qualquer grupo de recursos cujo nome termina em `*netrg`.

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

Este exemplo de regra de política utiliza **valor** para verificar se o resultado de vários aninhar funções **é igual a** `true`. A regra nega qualquer recurso que não tem, pelo menos, três etiquetas.

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

#### <a name="avoiding-template-failures"></a>Evitar falhas de modelo

A utilização de _funções de modelo_ na **valor** permite muitas funções aninhadas complexas. Se o resultado de uma _função de modelo_ é um erro, a falha de avaliação da política. Uma avaliação com falhas é uma implícita **negar**. Um exemplo de um **valor** que falha em determinados cenários:

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

A regra de política de exemplo acima utiliza [substring()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) para comparar os três primeiros carateres de **nome** para **abc**. Se **name** é menor do que três carateres, a `substring()` função resulta num erro. Este erro faz com que a política para se tornar um **negar** efeito.

Em alternativa, utilize o [surgirem](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) função para verificar se os três primeiros carateres de **nome** igual **abc** sem permitir um **nome** menor do que três carateres para fazer com que um erro:

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

Com a regra de política revisado `if()` verifica o comprimento do **nome** antes de tentar obter um `substring()` num valor com menos de três carateres. Se **name** é demasiado curto, o valor "não iniciar com abc" é devolvido em vez disso e em comparação com **abc**. Um recurso com um nome abreviado que não começa com **abc** continuar a falhar a regra de política, mas já não causa um erro durante a avaliação.

### <a name="effect"></a>Efeito

Política suporta os seguintes tipos de efeito:

- **Negar**: gera um evento no registo de atividades e falha do pedido
- **Auditoria**: gera um evento de aviso no registo de atividades, mas não falhar o pedido
- **Acrescentar**: Adiciona o conjunto definido de campos ao pedido
- **AuditIfNotExists**: permite a auditoria se não existir um recurso
- **DeployIfNotExists**: implementa um recurso, se ainda não exista
- **Desativado**: não avaliar os recursos de conformidade para a regra de política

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

Para obter detalhes completos sobre cada efeito, a ordem de avaliação, propriedades e exemplos, consulte [efeitos de política de compreensão](effects.md).

### <a name="policy-functions"></a>Funções de política

Todos os [funções de modelo do Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) estão disponíveis para uso dentro de uma regra de política, exceto as seguintes funções:

- copyIndex()
- deployment()
- list*
- Providers()
- reference()
- resourceId()
- variables()

Além disso, o `field` função está disponível para as regras de política. `field` é utilizado principalmente com **AuditIfNotExists** e **DeployIfNotExists** aos campos de referência no recurso que estão a ser avaliados. Um exemplo desta utilização pode ser visto na [DeployIfNotExists exemplo](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Exemplo de política de função

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

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzPolicyAlias -NamespaceMatch 'automation'
  ```

- CLI do Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Noções básicas sobre o alias [*]

Vários dos aliases que estão disponíveis possuem uma versão que é apresentado como um nome de "normal" e outro que tenha **[\*]** ligados ao mesmo. Por exemplo:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

O alias "normal" representa o campo como um único valor. Este campo é para cenários de comparação de correspondência exata quando todo o conjunto de valores tem de ser exatamente conforme definido, nem mais, nem menos. Usando **ipRules**, um exemplo seria a validar que um conjunto de regras existe, incluindo o número de regras e como montar cada regra. Esta regra de exemplo verifica a existência de exatamente ambos **192.168.1.1** e **10.0.4.1** com _ação_ de **permitir** na **ipRules** para aplicar a **effectType**:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "Equals": [
                    {
                        "action": "Allow",
                        "value": "192.168.1.1"
                    },
                    {
                        "action": "Allow",
                        "value": "10.0.4.1"
                    }
                ]
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

O **[\*]** alias torna possível a comparar com o valor de cada elemento na matriz e as propriedades específicas de cada elemento. Essa abordagem torna possível comparar as propriedades do elemento de "se nenhuma das', 'se qualquer um dos", ou "se todos os de" cenários. Usando **ipRules [\*]**, um exemplo seria possível validar que cada _ação_ é _negar_, mas não se preocupar sobre quantos regras existem ou que o IP _valor_ é. Esta regra de exemplo verifica a existência de quaisquer correspondências de **ipRules [\*]. Value** para **10.0.4.1** e aplica-se a **effectType** apenas se não encontrar, pelo menos, uma correspondência:

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

Para obter mais informações, consulte [avaliar o [\*] alias](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

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

## <a name="next-steps"></a>Passos Seguintes

- Reveja exemplos em [exemplos do Azure Policy](../samples/index.md)
- Revisão [Noções básicas sobre os efeitos de política](effects.md)
- Compreender como [criar políticas programaticamente](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md)
- Saiba como [remediar recursos não compatíveis](../how-to/remediate-resources.md)
- Rever o que é um grupo de gestão, com [Organizar os recursos com grupos de gestão do Azure](../../management-groups/overview.md)

---
title: Funções de esquemas do Azure
description: Descreve as funções para utilização com as definições de esquemas do Azure e atribuições.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209416"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funções para utilização com esquemas do Azure

Planos gráficos do Azure fornece funções fazendo uma definição de esquema mais dinâmico. Estas funções são para utilização com as definições de esquema e artefactos de esquema. Um artefato de modelo do Resource Manager suporta a utilização completa de funções do Resource Manager, além de obter um valor dinâmico através de um parâmetro de esquema.

São suportadas as seguintes funções:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscrição](#subscription)

## <a name="artifacts"></a>Artefactos

`artifacts(artifactName)`

Devolve que um objeto de propriedades preenchido com esse artefactos de esquema saídas.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| artifactName |Sim |string |O nome do artefacto de esquema. |

### <a name="return-value"></a>Valor de retorno

Um objeto de propriedades de saída. O **produz** propriedades são dependentes do tipo de artefacto de esquema a ser referenciado. Todos os tipos de seguem o formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefacto de atribuição de política

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefactos de modelo do Resource Manager

O **produz** propriedades do objeto devolvido são definidas no modelo do Resource Manager e devolvidas pela implantação.

#### <a name="role-assignment-artifact"></a>Artefacto de atribuição de função

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Exemplo

Um artefato de modelo do Resource Manager com o ID _myTemplateArtifact_ que contém o exemplo a seguir a propriedade de saída:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Alguns exemplos de obter dados a partir da _myTemplateArtifact_ exemplo são:

| expressão | Tipo | Value |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "meu valor de cadeia de caracteres" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "meu valor" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Booleano | Verdadeiro |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina vários valores de cadeia de caracteres e retorna a cadeia de caracteres concatenada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| string1 |Sim |string |O primeiro valor para concatenação. |
| argumentos adicionais |Não |string |Valores adicionais na ordem sequencial, de concatenação |

### <a name="return-value"></a>Valor de retorno

Uma cadeia de valores concatenados.

### <a name="remarks"></a>Observações

A função de esquema do Azure é diferente da função de modelo do Azure Resource Manager, em que ele só funciona com cadeias de caracteres.

### <a name="example"></a>Exemplo

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Devolve um valor de parâmetro de esquema. O nome de parâmetro especificado tem de ser definido na definição do esquema ou em artefactos de esquema.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro para retornar. |

### <a name="return-value"></a>Valor de retorno

O valor do parâmetro de artefactos de esquema ou esquema especificada.

### <a name="remarks"></a>Observações

A função de esquema do Azure é diferente da função de modelo do Azure Resource Manager, em que ele só funciona com parâmetros de esquema.

### <a name="example"></a>Exemplo

Definir o parâmetro _principalIds_ na definição do esquema:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Em seguida, utilize _principalIds_ como o argumento `parameters()` num artefacto de esquema:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Devolve um objeto que representa o grupo de recursos atual.

### <a name="return-value"></a>Valor de retorno

O objeto devolvido é no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Observações

A função de esquema do Azure é diferente da função de modelo do Azure Resource Manager. O `resourceGroup()` função não é possível utilizar um artefato de nível de subscrição ou a definição do esquema. Só pode ser utilizado no artefactos de esquema que fazem parte de um artefacto de grupo de recursos.

Um uso comum do `resourceGroup()` função é criar recursos na mesma localização que o artefacto de grupo de recursos.

### <a name="example"></a>Exemplo

Para utilizar a localização do grupo de recursos, definido em qualquer um, a definição do esquema ou durante a atribuição, como a localização para outra artefacto, declaro um objeto de marcador de posição do grupo de recursos na sua definição de esquema. Neste exemplo, _NetworkingPlaceholder_ é o nome do marcador de posição do grupo de recursos.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Em seguida, utilize o `resourceGroup()` função no contexto de um artefacto de esquema que se destina a um objeto de marcador de posição do grupo de recursos. Neste exemplo, o artefacto de modelo é implementado para o _NetworkingPlaceholder_ grupo de recursos e fornece o parâmetro _resourceLocation_ dinamicamente preenchidos com o  _NetworkingPlaceholder_ localização do grupo de recursos para o modelo. A localização do _NetworkingPlaceholder_ grupo de recursos foi foram definido estaticamente na definição do esquema ou definido dinamicamente durante a atribuição. Em ambos os casos, o artefacto de modelo é fornecido essas informações como um parâmetro e a utiliza para implementar os recursos para a localização correta.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Devolve um objeto que representa o artefacto de grupo de recursos especificado. Ao contrário de `resourceGroup()`, que requer que o contexto do artefacto, esta função é usada para obter as propriedades de um marcador de posição de grupo de recursos específico quando não estiver no contexto do grupo de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| placeholderName |Sim |string |O nome do marcador de posição do artefacto de grupo de recursos a devolver. |

### <a name="return-value"></a>Valor de retorno

O objeto devolvido é no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemplo

Para utilizar a localização do grupo de recursos, definido em qualquer um, a definição do esquema ou durante a atribuição, como a localização para outra artefacto, declaro um objeto de marcador de posição do grupo de recursos na sua definição de esquema. Neste exemplo, _NetworkingPlaceholder_ é o nome do marcador de posição do grupo de recursos.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Em seguida, utilize o `resourceGroups()` função a partir do contexto de quaisquer artefactos de esquema para obter uma referência ao objeto de marcador de posição do grupo de recursos. Neste exemplo, o artefacto de modelo é implantado fora da _NetworkingPlaceholder_ grupo de recursos e fornece o parâmetro _artifactLocation_ dinamicamente preenchidos com o  _NetworkingPlaceholder_ localização do grupo de recursos para o modelo. A localização do _NetworkingPlaceholder_ grupo de recursos foi foram definido estaticamente na definição do esquema ou definido dinamicamente durante a atribuição. Em ambos os casos, o artefacto de modelo é fornecido essas informações como um parâmetro e a utiliza para implementar os recursos para a localização correta.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscrição

`subscription()`

Devolve detalhes sobre a subscrição para a atribuição do esquema atual.

### <a name="return-value"></a>Valor de retorno

O objeto devolvido é no seguinte formato:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exemplo

Utilize o nome a apresentar da subscrição e o `concat()` função para criar uma convenção de nomenclatura passada como parâmetro _resourceName_ para o artefacto de modelo.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [ciclo de vida de um esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).
---
title: Funções de Plantas Azure
description: Descreve as funções disponíveis para utilização com artefactos de planta em definições e atribuições de Plantas Azure.
ms.date: 01/27/2021
ms.topic: reference
ms.openlocfilehash: 92cb906e87179073b7a69aa0bd4eab22c77087f9
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919279"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funções para utilização com Plantas Azure

A Azure Blueprints fornece funções tornando uma definição de planta mais dinâmica. Estas funções são para uso com definições de planta e artefactos de planta. Um artefacto do Modelo do Gestor de Recursos Azure (modelo ARM) suporta o uso total das funções de Gestor de Recursos, além de obter um valor dinâmico através de um parâmetro de planta.

As seguintes funções são suportadas:

- [artefactos](#artifacts)
- [concat](#concat)
- [parâmetros](#parameters)
- [resourceGroup](#resourcegroup)
- [grupos de recursos](#resourcegroups)
- [subscrição](#subscription)

## <a name="artifacts"></a>artefactos

`artifacts(artifactName)`

Devolve um objeto de propriedades povoadas com as saídas de artefactos de plantas.

> [!NOTE]
> A `artifacts()` função não pode ser utilizada a partir de dentro de um modelo ARM. A função só pode ser utilizada na definição de planta JSON ou no artefacto JSON quando gerir a planta com Azure PowerShell ou REST API como parte de [Blueprints-as-code](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| nome de artefactos |Yes |string |O nome de um artefacto de planta. |

### <a name="return-value"></a>Valor devolvido

Um objeto de propriedades de saída. As propriedades **das saídas** dependem do tipo de artefacto de planta que está a ser referenciado. Todos os tipos seguem o formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefacto de atribuição de políticas

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>Artefacto do modelo ARM

As propriedades de saídas do objeto devolvido são **definidas** dentro do modelo ARM e devolvidas pela implementação.

#### <a name="role-assignment-artifact"></a>Artefacto de atribuição de funções

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

Um artefacto do modelo ARM com o _iD myTemplateArtifact_ contendo a seguinte propriedade de saída de amostra:

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

Alguns exemplos de obtenção de dados da amostra _myTemplateArtifact_ são:

| Expression | Tipo | Valor |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Matriz | \["Primeiro", "segundo"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "Primeiro" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "o meu valor de corda" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objeto | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "o meu valor" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Booleano | Verdadeiro |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina vários valores de corda e devolve a corda concatenated.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| string1 |Yes |string |O primeiro valor para a concatenação. |
| argumentos adicionais |No |string |Valores adicionais na ordem sequencial para a concatenação |

### <a name="return-value"></a>Valor devolvido

Uma série de valores concatenados.

### <a name="remarks"></a>Observações

A função Azure Blueprint difere da função do modelo ARM, na medida em que funciona apenas com cordas.

### <a name="example"></a>Exemplo

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parâmetros

`parameters(parameterName)`

Devolve um valor de parâmetro de planta. O nome do parâmetro especificado deve ser definido na definição da planta ou em artefactos de planta.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| nome de parâmetroName |Yes |string |O nome do parâmetro para voltar. |

### <a name="return-value"></a>Valor devolvido

O valor do parâmetro de planta ou artefacto de planta especificado.

### <a name="remarks"></a>Observações

A função Azure Blueprint difere da função do modelo ARM, na medida em que funciona apenas com parâmetros de planta.

### <a name="example"></a>Exemplo

Defina _os principados de parâmetros_ na definição do projeto:

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
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Em seguida, use _os principais_ como argumento para `parameters()` um artefacto de planta:

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

### <a name="return-value"></a>Valor devolvido

O objeto devolvido encontra-se no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Observações

A função Azure Blueprint difere da função do modelo ARM. A `resourceGroup()` função não pode ser usada num artefacto de nível de subscrição ou na definição de planta. Só pode ser usado em artefactos de planta que fazem parte de um artefacto de grupo de recursos.

Um uso comum da `resourceGroup()` função é criar recursos no mesmo local que o artefacto do grupo de recursos.

### <a name="example"></a>Exemplo

Para utilizar a localização do grupo de recursos, definido na definição de planta ou durante a atribuição, como a localização de outro artefacto, declare um objeto de espaço reservado de grupo de recursos na definição de planta. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado do grupo de recursos.

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

Em seguida, utilize a `resourceGroup()` função no contexto de um artefacto de planta que está a visar um objeto reservado de grupo de recursos. Neste exemplo, o artefacto do modelo é implantado no grupo de recursos _NetworkingPlaceholder_ e fornece recursos de _parâmetrosLocalização_ dinamicamente povoada com a localização do grupo de recursos _NetworkingPlaceholder_ para o modelo. A localização do grupo de recursos _NetworkingPlaceholder_ poderia ter sido estática definida na definição do projeto ou definida dinamicamente durante a atribuição. Em qualquer dos casos, o artefacto do modelo é fornecido que a informação como parâmetro e usa-a para implantar os recursos para a localização correta.

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

## <a name="resourcegroups"></a>grupos de recursos

`resourceGroups(placeholderName)`

Devolve um objeto que representa o artefacto do grupo de recursos especificado. Ao contrário de `resourceGroup()` , que requer contexto do artefacto, esta função é usada para obter as propriedades de um espaço reservado específico do grupo de recursos quando não no contexto desse grupo de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| nome de espaço reservado |Yes |string |O nome reservado do artefacto do grupo de recursos para regressar. |

### <a name="return-value"></a>Valor devolvido

O objeto devolvido encontra-se no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemplo

Para utilizar a localização do grupo de recursos, definido na definição de planta ou durante a atribuição, como a localização de outro artefacto, declare um objeto de espaço reservado de grupo de recursos na definição de planta. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado do grupo de recursos.

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

Em seguida, utilize a `resourceGroups()` função a partir do contexto de qualquer artefacto de planta para obter uma referência ao objeto reservado do grupo de recursos. Neste exemplo, o artefacto do modelo é implantado fora do grupo de recursos _NetworkingPlaceholder_ e fornece _artefactos_ de parâmetroLocalização dinâmicamente povoado com a localização do grupo de recursos _NetworkingPlaceholder_ para o modelo. A localização do grupo de recursos _NetworkingPlaceholder_ poderia ter sido estática definida na definição do projeto ou definida dinamicamente durante a atribuição. Em qualquer dos casos, o artefacto do modelo é fornecido que a informação como parâmetro e usa-a para implantar os recursos para a localização correta.

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

Devolve detalhes sobre a subscrição para a atribuição do projeto atual.

### <a name="return-value"></a>Valor devolvido

O objeto devolvido encontra-se no seguinte formato:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exemplo

Use o nome de exibição da subscrição e a `concat()` função para criar uma convenção de nomeação passada como recurso de _parâmetroName_ ao artefacto do modelo.

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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).
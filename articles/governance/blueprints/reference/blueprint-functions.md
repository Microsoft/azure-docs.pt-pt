---
title: Funções de Plantas Azure
description: Descreve as funções disponíveis para utilização com artefactos de plantas em definições e atribuições de Plantas Azure.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386245"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funções para utilização com plantas azure

A Azure Blueprints fornece funções tornando uma definição de planta mais dinâmica. Estas funções são para uso com definições de plantas e artefactos de plantas. Um artefacto de modelo de gestor de recursos suporta o uso total das funções do Gestor de Recursos, além de obter um valor dinâmico através de um parâmetro de planta.

As seguintes funções são suportadas:

- [artefactos](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [recursosGroup](#resourcegroup)
- [recursosGrupos](#resourcegroups)
- [subscrição](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Devolve um objeto de propriedades povoadas com as saídas de artefactos de plantas.

> [!NOTE]
> A função `artifacts()` não pode ser usada a partir de dentro de um modelo de gestor de recursos. A função só pode ser utilizada na definição de planta JSON ou no artefacto JSON ao gerir a planta com a Azure PowerShell ou a REST API como parte das [Plantas-como código](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| artifactName |Sim |string |O nome de um artefacto de planta. |

### <a name="return-value"></a>Valor de retorno

Um objeto de propriedades de saída. As propriedades de **saída** dependem do tipo de artefacto de planta que está a ser referenciado. Todos os tipos seguem o formato:

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

#### <a name="resource-manager-template-artifact"></a>Artefacto de modelo do gestor de recursos

As propriedades de **saída** do objeto devolvido são definidas dentro do modelo de Gestor de Recursos e devolvidas pela implementação.

#### <a name="role-assignment-artifact"></a>Artefacto de atribuição de papéis

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

Um artefacto de modelo de Gestor de Recursos com o _ID myTemplateArtifact_ contendo a seguinte propriedade de saída de amostra:

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

Alguns exemplos de recuperação de dados da amostra _myTemplateArtifact_ são:

| Expressão | Tipo | Valor |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["primeiro", "segundo"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "Primeiro" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "O meu valor de corda" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "O meu valor" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | Verdadeiro |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina múltiplos valores de cordas e devolve a corda concatenada.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| cadeia1 |Sim |string |O primeiro valor para a concatenação. |
| argumentos adicionais |Não |string |Valores adicionais na ordem sequencial de concatenação |

### <a name="return-value"></a>Valor de retorno

Uma série de valores concatenados.

### <a name="remarks"></a>Observações

A função Azure Blueprint difere da função do modelo do Gestor de Recursos Azure, na medida em que funciona apenas com cordas.

### <a name="example"></a>Exemplo

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parâmetros

`parameters(parameterName)`

Devolve um valor de parâmetro de planta. O nome do parâmetro especificado deve ser definido na definição da planta ou nos artefactos da planta.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro para voltar. |

### <a name="return-value"></a>Valor de retorno

O valor do parâmetro de plantas ou artefactos de planta especificado.

### <a name="remarks"></a>Observações

A função Azure Blueprint difere da função do modelo do Gestor de Recursos Azure, na medida em que funciona apenas com parâmetros de planta.

### <a name="example"></a>Exemplo

Definir _os principais parâmetros_ na definição de projeto:

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

Em seguida, utilize os _principados como_ argumento para `parameters()` num artefacto de plantas:

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

A função Azure Blueprint difere da função de modelo do Gestor de Recursos Azure. A função `resourceGroup()` não pode ser usada num artefacto de nível de subscrição ou na definição de planta. Só pode ser usado em artefactos de plantas que fazem parte de um artefacto de grupo de recursos.

Um uso comum da função `resourceGroup()` é criar recursos no mesmo local que o artefacto do grupo de recursos.

### <a name="example"></a>Exemplo

Para utilizar a localização do grupo de recursos, definida na definição de planta ou durante a atribuição, como localização para outro artefacto, declare um objeto de espaço reservado de grupo de recursos na definição de planta. Neste exemplo, o Suporte à _Rede Placeholder_ é o nome do espaço reservado do grupo de recursos.

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

Em seguida, utilize a função `resourceGroup()` no contexto de um artefacto de planta que está a visar um objeto de espaço reservado de grupo de recursos. Neste exemplo, o artefacto do modelo é implantado no grupo de recursos _NetworkingPlaceholder_ e fornece recursos paramétricosLocalização dinamicamente povoada com a localização do grupo de recursos _NetworkingPlaceholder_ para o modelo. A localização do grupo de recursos _NetworkingPlaceholder_ poderia ter sido definida estáticamente na definição de projeto ou definida dinamicamente durante a atribuição. Em qualquer dos casos, o artefacto do modelo é fornecido essa informação como parâmetro e usa-a para implantar os recursos para a localização correta.

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

## <a name="resourcegroups"></a>recursosGrupos

`resourceGroups(placeholderName)`

Devolve um objeto que representa o artefacto do grupo de recursos especificado. Ao contrário `resourceGroup()`, que requer contexto do artefacto, esta função é usada para obter as propriedades de um espaço reservado de um grupo de recursos específico quando não está no contexto desse grupo de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Necessário | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| placeholderName |Sim |string |O nome do espaço reservado do artefacto do grupo de recursos para regressar. |

### <a name="return-value"></a>Valor de retorno

O objeto devolvido é no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemplo

Para utilizar a localização do grupo de recursos, definida na definição de planta ou durante a atribuição, como localização para outro artefacto, declare um objeto de espaço reservado de grupo de recursos na definição de planta. Neste exemplo, o Suporte à _Rede Placeholder_ é o nome do espaço reservado do grupo de recursos.

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

Em seguida, utilize a função `resourceGroups()` a partir do contexto de qualquer artefacto de planta para obter uma referência ao objeto de espaço reservado do grupo de recursos. Neste exemplo, o artefacto do modelo é implantado fora do grupo de recursos _NetworkingPlaceholder_ e fornece _artefactos_ paramétricosLocalização dinamicamente povoada com a localização do grupo de recursos _NetworkingPlaceholder_ para o modelo. A localização do grupo de recursos _NetworkingPlaceholder_ poderia ter sido definida estáticamente na definição de projeto ou definida dinamicamente durante a atribuição. Em qualquer dos casos, o artefacto do modelo é fornecido essa informação como parâmetro e usa-a para implantar os recursos para a localização correta.

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

Utilize o nome de exibição da subscrição e a função `concat()` para criar uma convenção de nomeação aprovada como recurso de _parâmetronome nome_ para o artefacto do modelo.

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
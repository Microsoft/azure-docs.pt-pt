---
title: Detalhes da estrutura de atribuição de políticas
description: Descreve a definição de atribuição de políticas utilizada pela Azure Policy para relacionar definições de políticas e parâmetros com recursos para avaliação.
ms.date: 03/17/2021
ms.topic: conceptual
ms.openlocfilehash: 909c1c361e092c512a73854a40e22a67efe5f2f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604870"
---
# <a name="azure-policy-assignment-structure"></a>Estrutura de atribuição do Azure Policy

As atribuições políticas são utilizadas pela Azure Policy para definir quais os recursos atribuídos que políticas ou iniciativas. A atribuição de políticas pode determinar os valores dos parâmetros para esse grupo de recursos no momento da atribuição, possibilitando a reutilização de definições de políticas que abordam as mesmas propriedades de recursos com diferentes necessidades de cumprimento.

Usas o JSON para criar uma missão política. A atribuição de políticas contém elementos para:

- nome de exibição
- descrição
- do IdP
- modo de execução
- âmbitos excluídos
- definição de política
- mensagens de incumprimento
- parâmetros

Por exemplo, o seguinte JSON mostra uma atribuição de política no modo _DoNotEnforce_ com parâmetros dinâmicos:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "notScopes": [],
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "nonComplianceMessages": [
            {
                "message": "Resource names must start with 'DeptA' and end with '-LC'."
            }
        ],
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

Todas as amostras da Política Azure estão nas [amostras da Azure Policy](../samples/index.md).

## <a name="display-name-and-description"></a>Nome e descrição do visor

Utiliza o nome e **a descrição** **do displayName** para identificar a atribuição de políticas e fornecer contexto para a sua utilização com o conjunto específico de recursos. **displayName** tem um comprimento máximo de _128_ caracteres e **descrição** de um comprimento máximo de _512_ caracteres.

## <a name="enforcement-mode"></a>Modo de Execução

A **propriedade EnforcementMode** proporciona aos clientes a capacidade de testar o resultado de uma política sobre os recursos existentes sem iniciar o efeito de política ou desencadear entradas no registo de [Atividades Azure](../../../azure-monitor/essentials/platform-logs-overview.md). Este cenário é comumente referido como "E Se" e alinha-se com práticas de implementação seguras. **o controlo do modo de aplicação** é diferente do efeito [de desativado,](./effects.md#disabled) uma vez que esse efeito impede que a avaliação dos recursos aconteça.

Esta propriedade tem os seguintes valores:

|Modo |Valor JSON |Tipo |Remediar manualmente |Entrada de registo de atividade |Description |
|-|-|-|-|-|-|
|Ativado |Predefinição |string |Yes |Yes |O efeito da política é aplicado durante a criação ou atualização de recursos. |
|Desativado |DoNotEnforce |string |Yes |No | O efeito da política não é aplicado durante a criação ou atualização de recursos. |

Se **o número de execução Não** for especificado numa definição de política ou iniciativa, o valor _Padrão_ é utilizado. [As tarefas de reparação](../how-to/remediate-resources.md) podem ser iniciadas para implementar as [políticasifNotExists,](./effects.md#deployifnotexists) mesmo quando **a aplicação doMode** está definida para _DoNotEnforce_.

## <a name="excluded-scopes"></a>Âmbitos excluídos

O **âmbito** da atribuição inclui todos os contentores de recursos infantis e recursos para crianças. Se um recipiente de recursos para crianças ou um recurso para crianças não deverão ter a definição aplicada, cada um pode ser _excluído_ da avaliação definindo **nãoScopes**. Esta propriedade é um conjunto que permite excluir um ou mais contentores de recursos ou recursos da avaliação. **não Osscópios** podem ser adicionados ou atualizados após a criação da atribuição inicial.

> [!NOTE]
> Um recurso _excluído_ é diferente de um recurso _isento._ Para mais informações, consulte [o âmbito de aplicação da Política Azure.](./scope.md)

## <a name="policy-definition-id"></a>ID de definição de política

Este campo deve ser o nome completo de uma definição de política ou de uma definição de iniciativa.
`policyDefinitionId` é uma corda e não uma matriz. Recomenda-se que, se várias políticas forem muitas vezes atribuídas em conjunto, use uma [iniciativa](./initiative-definition-structure.md) em vez disso.

## <a name="non-compliance-messages"></a>Mensagens de incumprimento

Para definir uma mensagem personalizada que descreva por que um recurso não está em conformidade com a definição de política ou iniciativa, definida `nonComplianceMessages` na definição de atribuição. Este nó é uma variedade de `message` entradas. Esta mensagem personalizada é além da mensagem de erro por defeito para incumprimento e é opcional.

> [!IMPORTANT]
> As mensagens personalizadas para incumprimento são suportadas apenas em definições ou iniciativas com [definições de modos gestor de recursos.](./definition-structure.md#resource-manager-modes)

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    }
]
```

Se a atribuição for para uma iniciativa, podem ser configuradas diferentes mensagens para cada definição de política na iniciativa. As mensagens utilizam o `policyDefinitionReferenceId` valor configurado na definição de iniciativa. Para mais detalhes, consulte [as propriedades de definições de política.](./initiative-definition-structure.md#policy-definition-properties)

```json
"nonComplianceMessages": [
    {
        "message": "Default message"
    },
    {
        "message": "Message for just this policy definition by reference ID",
        "policyDefinitionReferenceId": "10420126870854049575"
    }
]
```

## <a name="parameters"></a>Parâmetros

Este segmento da atribuição de políticas fornece os valores para os parâmetros definidos na [definição de política ou na definição de iniciativa.](./definition-structure.md#parameters) Este desenho permite reutilizar uma definição de política ou iniciativa com diferentes recursos, mas verificar diferentes valores ou resultados de negócio.

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

Neste exemplo, os parâmetros previamente definidos na definição de política são `prefix` e `suffix` . Esta atribuição de política específica `prefix` define-se à **DeptA** e `suffix` à **-LC**. A mesma definição política é reutilizável com um conjunto diferente de parâmetros para um departamento diferente, reduzindo simultanea a duplicação e a complexidade das definições políticas, proporcionando flexibilidade.

## <a name="next-steps"></a>Passos seguintes

- Conheça a estrutura de [definição de políticas.](./definition-structure.md)
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)

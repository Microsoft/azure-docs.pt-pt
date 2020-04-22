---
title: Detalhes da estrutura de atribuição de políticas
description: Descreve a definição de atribuição de políticas utilizada pela Política Azure para relacionar definições de políticas e parâmetros aos recursos para avaliação.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: cdb2fc0c6f057ece44383f68bc79fca54507db9b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683208"
---
# <a name="azure-policy-assignment-structure"></a>Estrutura de atribuição do Azure Policy

As atribuições políticas são utilizadas pela Política Azure para definir quais os recursos atribuídos quais as políticas ou iniciativas. A atribuição de políticas pode determinar os valores dos parâmetros para esse grupo de recursos no momento da atribuição, possibilitando a reutilização de definições políticas que abordam as mesmas propriedades de recursos com diferentes necessidades de conformidade.

Usas a JSON para criar uma missão política. A atribuição de políticas contém elementos para:

- nome de exibição
- descrição
- do IdP
- modo de execução
- âmbitos excluídos
- definição política
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

Todas as amostras da Política Azure estão em amostras da [Política Azure.](../samples/index.md)

## <a name="display-name-and-description"></a>Nome e descrição do mostrador

Utiliza o **displayName** e **descrição** para identificar a atribuição de políticas e fornecer contexto para a sua utilização com o conjunto específico de recursos. **displayName** tem um comprimento máximo de _128_ caracteres e **descrição** de um comprimento máximo de _512_ caracteres.

## <a name="enforcement-mode"></a>Modo de Execução

A propriedade **enforcementMode** fornece aos clientes a capacidade de testar o resultado de uma política sobre os recursos existentes sem iniciar o efeito político ou desencadear entradas no registo de [Atividade seletiva](../../../azure-monitor/platform/platform-logs-overview.md). Este cenário é comumente referido como "E Se" e alinha-se com práticas de implantação seguras. o modo de **execução** é diferente do efeito [Desativado,](./effects.md#disabled) uma vez que esse efeito impede que a avaliação dos recursos aconteça.

Esta propriedade tem os seguintes valores:

|Modo |Valor JSON |Tipo |Remediar manualmente |Entrada de registo de atividade |Descrição |
|-|-|-|-|-|-|
|Ativado |Predefinição |string |Sim |Sim |O efeito político é aplicado durante a criação de recursos ou a atualização. |
|Desativado |DonotEnforce |string |Sim |Não | O efeito político não é aplicado durante a criação de recursos ou atualização. |

Se o **modo de execução** Não for especificado numa definição de política ou de iniciativa, o valor _Padrão_ é utilizado. As tarefas de [reparação](../how-to/remediate-resources.md) podem ser iniciadas para implementar políticas [IfNotExists,](./effects.md#deployifnotexists) mesmo quando o modo de **execução** está definido para _DoNotEnforce_.

## <a name="excluded-scopes"></a>Âmbitos excluídos

O **âmbito** da atribuição inclui todos os contentores de recursos infantis e recursos infantis. Se um recipiente de recursos para crianças ou um recurso para crianças não tiver a definição aplicada, cada um pode ser excluído da avaliação definindo **nãoScopes**. Esta propriedade é uma matriz para permitir excluir um ou mais contentores de recursos ou recursos da avaliação. **notScopes** podem ser adicionados ou atualizados após a criação da atribuição inicial.

## <a name="policy-definition-id"></a>ID de definição de política

Este domínio deve ser o nome completo de uma definição de política ou de uma definição de iniciativa.
`policyDefinitionId`é uma corda e não uma matriz. Recomenda-se que, se várias políticas forem muitas vezes atribuídas em conjunto, para usar uma [iniciativa](./definition-structure.md#initiatives) em vez disso.

## <a name="parameters"></a>Parâmetros

Este segmento da atribuição de políticas fornece os valores para os parâmetros definidos na [definição de política ou definição](./definition-structure.md#parameters)de iniciativa.
Este desenho permite reutilizar uma definição de política ou iniciativa com diferentes recursos, mas verificar se existem diferentes valores ou resultados de negócio.

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

Neste exemplo, os parâmetros previamente definidos `prefix` `suffix`na definição de política são e . Esta atribuição de `prefix` políticas específicas `suffix` define-se para **depta** e **para -LC**. A mesma definição de política é reutilizável com um conjunto diferente de parâmetros para um departamento diferente, reduzindo a duplicação e a complexidade das definições políticas, proporcionando simultaneamente flexibilidade.

## <a name="next-steps"></a>Passos seguintes

- Conheça a estrutura da definição de [políticas.](./definition-structure.md)
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)
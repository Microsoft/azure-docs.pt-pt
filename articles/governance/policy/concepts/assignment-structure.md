---
title: Detalhes da estrutura de atribuição de política
description: Descreve a definição de atribuição de política usada por Azure Policy para relacionar definições de política e parâmetros a recursos para avaliação.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: f03c654dfc4c8dfdf2bdc5103a5961b4d8ce1e64
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747212"
---
# <a name="azure-policy-assignment-structure"></a>Estrutura de atribuição do Azure Policy

As atribuições de política são usadas pelo Azure Policy para definir quais recursos são atribuídos a quais políticas ou iniciativas. A atribuição de política pode determinar os valores dos parâmetros para esse grupo de recursos no momento da atribuição, possibilitando a reutilização de definições de política que abordam as mesmas propriedades de recurso com necessidades diferentes de conformidade.

Você usa JSON para criar uma atribuição de política. A atribuição de política contém elementos para:

- Nome a apresentar
- descrição
- do IdP
- modo de imposição
- definição de política
- parâmetros

Por exemplo, o JSON a seguir mostra uma atribuição de política no modo _DoNotEnforce_ com parâmetros dinâmicos:

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
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

Todos os exemplos de Azure Policy estão em [exemplos de Azure Policy](../samples/index.md).

## <a name="display-name-and-description"></a>Nome a apresentar e descrição

Use **DisplayName** e **Description** para identificar a atribuição de política e fornecer contexto para seu uso com o conjunto específico de recursos. **DisplayName** tem um comprimento máximo de _128_ caracteres e a **Descrição** é de um comprimento máximo de _512_ caracteres.

## <a name="enforcement-mode"></a>Modo de imposição

A **Propriedade** impolicymode fornece aos clientes a capacidade de testar o resultado de uma política em recursos existentes sem iniciar o efeito da política ou disparar entradas no [log de atividades do Azure](../../../azure-monitor/platform/platform-logs-overview.md). Esse cenário é conhecido como "What If" e alinha-se às práticas de implantação seguras. **imposiçãomode** é diferente do efeito [desabilitado](./effects.md#disabled) , pois esse efeito impede que a avaliação de recursos aconteça.

Essa propriedade tem os seguintes valores:

|Modo |Valor JSON |Tipo |Corrigir manualmente |Entrada do log de atividades |Descrição |
|-|-|-|-|-|-|
|Ativado |Predefinição |string |Sim |Sim |O efeito de política é imposto durante a criação ou atualização de recursos. |
|Desativado |DoNotEnforce |string |Sim |Não | O efeito de política não é imposto durante a criação ou atualização de recursos. |

Se **imposiçãomode** não for especificado em uma definição de política ou iniciativa, o valor _padrão_ será usado. [As tarefas de correção](../how-to/remediate-resources.md) podem ser iniciadas para políticas de [deployIfNotExists](./effects.md#deployifnotexists) , **mesmo quando** é definido como _DoNotEnforce_.

## <a name="policy-definition-id"></a>ID de definição de política

Este campo deve ser o nome do caminho completo de uma definição de política ou uma definição de iniciativa.
`policyDefinitionId` é uma cadeia de caracteres e não uma matriz. É recomendável que, em vez disso, várias políticas sejam atribuídas juntas, para usar uma [iniciativa](./definition-structure.md#initiatives) .

## <a name="parameters"></a>Parâmetros

Esse segmento da atribuição de política fornece os valores para os parâmetros definidos na definição de [política ou definição de iniciativa](./definition-structure.md#parameters).
Esse design torna possível reutilizar uma definição de política ou iniciativa com recursos diferentes, mas verificar valores comerciais ou resultados diferentes.

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

Neste exemplo, os parâmetros definidos anteriormente na definição de política são `prefix` e `suffix`. Essa atribuição de política específica define `prefix` para o **depta** e `suffix` a **-LC**. A mesma definição de política é reutilizável com um conjunto diferente de parâmetros para um departamento diferente, reduzindo a duplicação e a complexidade das definições de política, oferecendo flexibilidade.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [estrutura de definição de política](./definition-structure.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
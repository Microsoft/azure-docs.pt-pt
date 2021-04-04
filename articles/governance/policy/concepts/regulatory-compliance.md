---
title: Conformidade Regulamentar nas definições de iniciativa
description: Descreve como usar uma definição de iniciativa para agrupar políticas por domínio regulamentar como Controlo de Acesso, Gestão de Configurações, entre outros.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 15b03b5a40cba1290859b02883df70e3df54c5cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89645535"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Conformidade Regulamentar na Política Azure

A Normação de Conformidade na Política Azure fornece definições de iniciativa incorporadas para visualizar uma lista dos **domínios** de **controlo** e conformidade com base na responsabilidade _(Cliente,_ _Microsoft,_ _Shared_).
Para os controlos responsáveis da Microsoft, fornecemos detalhes adicionais dos nossos resultados de auditoria com base em atestados de terceiros e nos nossos detalhes de implementação para alcançar essa conformidade.
Os controlos responsáveis pela Microsoft são de `type` [estática.](./definition-structure.md#type)

> [!NOTE]
> Conformidade Regulamentar é uma funcionalidade de pré-visualização. Para os incorporados atualizados, as iniciativas controlam o mapa para a norma de conformidade correspondente. As iniciativas padrão de conformidade existentes estão em vias de ser atualizadas para apoiar a Conformidade Regulamentar.

## <a name="regulatory-compliance-defined"></a>Conformidade Regulamentar definida

A Conformidade Regulamentar baseia-se na parte de [agrupamento](./initiative-definition-structure.md#policy-definition-groups) de uma definição de iniciativa. Em incorporados, cada agrupamento na definição de iniciativa define um nome **(controlo**), uma categoria **(domínio de conformidade),** e fornece uma referência ao objeto [policyMetadata](./initiative-definition-structure.md#metadata-objects) que tem informações sobre esse **controlo.** Uma definição de iniciativa de conformidade regulamentar deve ter o `category` imóvel definido para **conformidade regulamentar.** Como definição de iniciativa padrão de outra forma, as iniciativas de Conformidade Regulamentar apoiam [parâmetros](./initiative-definition-structure.md#parameters) para criar atribuições dinâmicas.

Os clientes podem criar as suas próprias iniciativas de Conformidade Regulamentar. Estas definições podem ser originais ou copiadas a partir de definições incorporadas existentes. Se utilizar uma definição de iniciativa de conformidade regulamentar incorporada como referência, é aconselhável monitorizar a origem das definições de Conformidade Regulamentar no [repo da Política Azure GitHub](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance).

Para associar uma iniciativa de Conformidade Regulamentar personalizada ao seu painel de instrumentos do Azure Security Center, consulte [o Centro de Segurança Azure - Utilizando políticas de segurança personalizadas](../../../security-center/custom-security-policies.md).

## <a name="regulatory-compliance-in-portal"></a>Conformidade Regulamentar no portal

Quando uma definição de iniciativa foi criada com [grupos,](./initiative-definition-structure.md#policy-definition-groups)a página de detalhes **de Conformidade** no portal para esta iniciativa tem informações adicionais. 

Um novo separador, **Controls** é adicionado à página. A filtragem está disponível por **domínio de conformidade** e as definições de política são agrupadas pelo campo a partir do objeto `title` **policyMetadata.** Cada linha representa um **controlo** que mostra o seu estado de conformidade, o domínio de **conformidade** de que faz parte, informações de responsabilidade, e quantas definições de política não conformes e conformes compõem esse **controlo.**

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="Screenshot da visão geral da conformidade regulamentar para a definição incorporada NIST SP 800-53 R4 mostrando controlos compatíveis e não conformes.":::

A seleção de um **controlo** abre uma página de detalhes sobre esse controlo. A **visão geral** contém a informação de `description` e `requirements` . No âmbito do separador **Políticas** estão todas as definições políticas individuais na iniciativa que contribuem para este **controlo.** O **separador de conformidade de recursos** fornece uma visão granular de cada recurso que é avaliado por uma política de membros do **controlo** atualmente visualizado .

> [!NOTE]
> Um tipo de avaliação gerida **pela Microsoft** é para uma definição de política [estática.](./definition-structure.md#type) `type`

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="Screenshot dos detalhes da conformidade regulamentar para o controlo de proteção de fronteiras da definição incorporada NIST SP 800-53 R4.":::

A partir da mesma página **de controlo,** a alteração para o **separador de conformidade de Recursos** mostra todos os recursos que as definições de política do **controlo** incluem. Os filtros estão disponíveis para nome ou ID, estado de conformidade, tipo de recurso e localização.

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="Screenshot da conformidade dos recursos com o controlo de proteção de fronteiras da definição incorporada NIST SP 800-53 R4.":::

## <a name="regulatory-compliance-in-sdk"></a>Conformidade Regulamentar em SDK

Se a Conformidade Regulamentar estiver ativada numa definição de iniciativa, o registo de verificação de avaliação, eventos e políticas declaram SDK cada imóvel de retorno. Estas propriedades adicionais são agrupadas pelo estado de conformidade e fornecem informações sobre quantos grupos estão em cada estado.

O seguinte código é um exemplo de resultados adicionados de uma `summarize` chamada:

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>Passos seguintes

- Consulte a estrutura de [definição de iniciativa](./initiative-definition-structure.md)
- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Veja [Compreender os efeitos do Policy](./effects.md).
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)

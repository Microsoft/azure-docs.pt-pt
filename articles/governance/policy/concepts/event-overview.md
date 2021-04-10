---
title: Reagir aos acontecimentos de mudança de estado da Política Azure
description: Utilize a Azure Event Grid para subscrever eventos da App Policy, que permitem que as aplicações reajam a alterações de estado sem a necessidade de um código complicado.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735098"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Reagir aos acontecimentos de mudança de estado da Política Azure

Os eventos da Política Azure permitem que as aplicações reajam às mudanças de estado. Esta integração é feita sem a necessidade de um código complicado ou de serviços de sondagens dispendiosos e ineficientes. Em vez disso, os eventos são empurrados através [da Azure Event Grid](../../../event-grid/index.yml) para assinantes como [Azure Functions](../../../azure-functions/index.yml), [Azure Logic Apps](../../../logic-apps/index.yml), ou até mesmo para o seu próprio ouvinte personalizado.
Criticamente, só se paga pelo que se usa.

Os eventos da Azure Policy são enviados para a Azure Event Grid, que fornece serviços de entrega fiáveis às suas aplicações através de políticas de relagem ricas e entrega de cartas mortas. Para saber mais, consulte a [entrega de mensagens de Event Grid e retentou.](../../../event-grid/delivery-and-retry.md)

O cenário comum do evento Azure Policy está a acompanhar quando o estado de conformidade de um recurso muda durante a avaliação de políticas. A arquitetura baseada em eventos é uma forma eficiente de reagir a estas mudanças em vez de digitalizar o estado de conformidade dos recursos em um horário fixo.

> [!NOTE]
> Os eventos de mudança de estado da Azure Policy são enviados para a Grade de Eventos após um [gatilho de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de avaliação de recursos.

Consulte [os eventos de alteração do estado da rota para a Grade de Eventos com O Azure CLI](../tutorials/route-state-change-events.md) para um tutorial completo.

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Modelo de grelha de eventos de fontes e manipuladores" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Eventos de Política Azure disponíveis

A grelha de eventos utiliza [subscrições de eventos](../../../event-grid/concepts.md#event-subscriptions) para encaminhar mensagens de evento para assinantes. As subscrições de eventos da Azure Policy podem incluir três tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.PolicyInsights.PolicyStateCreated | Levantado quando um estado de conformidade política é criado. |
| Microsoft.PolicyInsights.PolicyStateChanged | Levantado quando um estado de conformidade com a política é alterado. |
| Microsoft.PolicyInsights.PolicyStateDeleted | Levantado quando um estado de conformidade com a política é eliminado. |

## <a name="event-schema"></a>Esquema de eventos

Os eventos da Azure Policy contêm toda a informação necessária para responder às alterações nos seus dados. Pode identificar um evento Azure Policy quando a `eventType` propriedade começa com "Microsoft.PolicyInsights".
Informações adicionais sobre o uso das propriedades do evento Event Grid são documentadas em  
[Esquema de eventos da Grelha de Eventos](../../../event-grid/event-schema.md).

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| `id` | cadeia (de carateres) | Identificador único para o evento. |
| `topic` | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| `subject` | string | O ID totalmente qualificado do recurso para o qual o estado de conformidade muda, incluindo o nome do recurso e o tipo de recurso. Usa o formato, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | objeto | Dados do evento Azure Policy. |
| `data.timestamp` | string | O tempo (na UTC) que o recurso foi digitalizado pela Azure Policy. Para encomendar eventos, use esta propriedade em vez do nível superior `eventTime` ou `time` propriedades. |
| `data.policyAssignmentId` | string | A identificação de recursos da atribuição de apólices. |
| `data.policyDefinitionId` | string | O documento de identificação de recursos da definição de política. |
| `data.policyDefinitionReferenceId` | string | A identificação de referência para a definição de política dentro da definição de iniciativa, se a atribuição de política for para uma iniciativa. Pode estar vazio. |
| `data.complianceState` | string | O estado de conformidade do recurso no que diz respeito à atribuição de políticas. |
| `data.subscriptionId` | string | A identificação de assinatura do recurso. |
| `data.complianceReasonCode` | string | O código de conformidade da razão. Pode estar vazio. |
| `eventType` | string | Um dos tipos de eventos registados para esta origem de evento. |
| `eventTime` | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| `dataVersion` | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| `metadataVersion` | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

Aqui está um exemplo de um evento de mudança de estado de política:

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Para mais informações, consulte [o esquema de eventos da Azure Policy.](../../../event-grid/event-schema-policy.md)

## <a name="practices-for-consuming-events"></a>Práticas para consumir eventos

As aplicações que lidam com eventos da Azure Policy devem seguir estas práticas recomendadas:

> [!div class="checklist"]
> - Várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, por isso não assuma que os eventos são de uma determinada fonte. Em vez disso, verifique o tópico da mensagem para garantir a atribuição de políticas, definição de política e recurso para o evento de mudança de estado.
> - Verifique o `eventType` e não assuma que todos os eventos que recebe são os tipos que espera.
> - Utilize `data.timestamp` para determinar a ordem dos eventos na Política Azure, em vez dos imóveis de nível superior ou de `eventTime` `time` propriedade.
> - Utilize o campo de assunto para aceder ao recurso que teve uma mudança de estado de política.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a Grade de Eventos e dê ao Azure Policy eventos de mudança de estado uma tentativa:

- [Rota política estado mudar eventos para Grade de Eventos com Azure CLI](../tutorials/route-state-change-events.md)
- [Detalhes do esquema da política Azure para a Grelha de Eventos](../../../event-grid/event-schema-policy.md)
- [Sobre o Event Grid](../../../event-grid/overview.md)
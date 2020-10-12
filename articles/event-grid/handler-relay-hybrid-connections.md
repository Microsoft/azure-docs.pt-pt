---
title: Conexão Relay Hybrid como manipulador de eventos para eventos Azure Event Grid
description: Descreve como pode utilizar ligações híbridas Azure Relay como manipuladores de eventos para eventos Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 38233a2e103600f07837ce9a1ad8d63fe7e4fb99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91270207"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Conexão Relay Hybrid como manipulador de eventos para eventos Azure Event Grid
Um manipulador de eventos é o local onde o evento é enviado. O manipulador toma mais algumas medidas para processar o evento. Vários serviços Azure são automaticamente configurados para lidar com eventos e **a Azure Relay** é um deles. 

Utilize **ligações híbridas** Azure Relay para enviar eventos para aplicações que estão dentro de uma rede empresarial e não têm um ponto final acessível ao público.

## <a name="tutorials"></a>Tutoriais
Consulte o seguinte tutorial para um exemplo de utilização de uma ligação híbrida Azure Relay como manipulador de eventos. 

|Título  |Descrição  |
|---------|---------|
| [Tutorial: enviar eventos para a conexão híbrida](custom-event-to-hybrid-connection.md) | Envia um evento personalizado para uma conexão híbrida existente para processamento através de uma aplicação de ouvinte. |

## <a name="rest-example-for-put"></a>Exemplo DE REPOUSO (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
---
title: Conexão Relay Hybrid como manipulador de eventos para eventos Azure Event Grid
description: Descreve como pode utilizar ligações híbridas Azure Relay como manipuladores de eventos para eventos Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83800888"
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

## <a name="next-steps"></a>Próximos passos
Consulte o artigo [dos manipuladores do Evento](event-handlers.md) para obter uma lista de manipuladores de eventos suportados. 
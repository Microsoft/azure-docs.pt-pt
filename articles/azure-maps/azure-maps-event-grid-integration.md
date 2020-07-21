---
title: Reagir aos eventos do Azure Maps utilizando a Grade de Eventos
description: Neste artigo, você aprenderá a reagir aos eventos do Microsoft Azure Maps usando a Grade de Eventos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: eb64634f25564abc4044364950b4d462a22608aa
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499516"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagir aos eventos do Azure Maps utilizando a Grade de Eventos

O Azure Maps integra-se com a Azure Event Grid, para que os utilizadores possam enviar notificações de eventos para outros serviços e desencadear processos a jusante. O objetivo deste artigo é ajudá-lo a configurar as suas aplicações de negócio para ouvir os eventos do Azure Maps. Isto permite que os utilizadores reajam a eventos críticos de forma fiável, escalável e segura. Por exemplo, os utilizadores podem construir uma aplicação para atualizar uma base de dados, criar um bilhete e entregar uma notificação por e-mail, sempre que um dispositivo introduz uma geofence.

A azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido, que utiliza um modelo de subscrição de publicação. A Event Grid tem suporte integrado para serviços Azure, como [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) e [Azure Logic Apps.](https://docs.microsoft.com/azure/azure-functions/functions-overview) Pode fornecer alertas de eventos para serviços não-Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que a Grade de Eventos suporta, consulte [uma introdução à Grelha de Eventos Azure](https://docs.microsoft.com/azure/event-grid/overview).


![Modelo funcional da Grelha de Eventos Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos Azure Maps

A grelha de eventos utiliza [subscrições de eventos](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) para encaminhar mensagens de evento para assinantes. Uma conta Azure Maps emite os seguintes tipos de eventos: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Levantado quando as coordenadas recebidas mudaram de fora de uma dada geofência para dentro |
| Microsoft.Maps.GeofenceExited | Levantado quando as coordenadas recebidas mudaram de dentro de uma dada geofência para fora |
| Microsoft.Maps.GeofenceResult | Levantado cada vez que uma consulta geofencing devolve um resultado, independentemente do estado |

## <a name="event-schema"></a>Esquema de eventos

O exemplo a seguir mostra o esquema de GeofenceResult:

```JSON
{
    "id":"451675de-a67d-4929-876c-5c2bf0b2c000",
    "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}",
    "subject":"/spatial/geofence/udid/{udid}/id/{eventId}",
    "data":{
        "geometries":[
            {
                "deviceId":"device_1",
                "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169",
                "geometryId":"1",
                "distance":999.0,
                "nearestLat":47.609833,
                "nearestLon":-122.148274
            }
        ],
        "expiredGeofenceGeometryId":[
        ],
        "invalidPeriodGeofenceGeometryId":[
        ]
    },
    "eventType":"Microsoft.Maps.GeofenceResult",
    "eventTime":"2018-11-08T00:52:08.0954283Z",
    "metadataVersion":"1",
    "dataVersion":"1.0"
}

```

## <a name="tips-for-consuming-events"></a>Sugestões para consumir eventos

As aplicações que lidam com eventos de geofência do Azure Maps devem seguir algumas práticas recomendadas:

* Configure várias subscrições para encaminhar eventos para o mesmo manipulador de eventos. É importante não assumir que os eventos são de uma fonte particular. Verifique sempre o tópico da mensagem para garantir que a mensagem veio da fonte que espera.
* Utilize o `X-Correlation-id` campo no cabeçalho de resposta para perceber se as suas informações sobre objetos estão atualizadas. As mensagens podem chegar desordenadas ou após um atraso.
* Quando um pedido GET ou POST na API de Geofence é chamado com o parâmetro de modo definido para `EnterAndExit` , então um evento de Entrada ou Saída é gerado para cada geometria na geofência para a qual o estado mudou a partir da chamada anterior da API de Geofence.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como usar geofencing para controlar operações em um estaleiro de construção, consulte:

> [!div class="nextstepaction"] 
> [Configurar uma geofence utilizando mapas Azure](tutorial-geofence.md)

---
title: Reagir aos eventos de mapa usando a Grelha de Eventos [ Microsoft Azure Maps
description: Neste artigo, aprenderá a reagir aos eventos do Microsoft Azure Maps utilizando a Event Grid.
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335723"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reaja aos eventos do Azure Maps usando a Grelha de Eventos 

O Azure Maps integra-se com a Azure Event Grid, para que os utilizadores possam enviar notificações de eventos para outros serviços e desencadear processos a jusante. O objetivo deste artigo é ajudá-lo a configurar as suas aplicações de negócio para ouvir eventos do Azure Maps. Isto permite que os utilizadores reajam a eventos críticos de forma fiável, escalável e segura. Por exemplo, os utilizadores podem construir uma aplicação para atualizar uma base de dados, criar um bilhete e entregar uma notificação por e-mail, sempre que um dispositivo entra numa geovete.

A Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido, que utiliza um modelo de subscrição de publicações. A Event Grid tem suporte integrado para serviços Azure como [Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) e [Aplicações Lógicas Azure.](https://docs.microsoft.com/azure/azure-functions/functions-overview) Pode fornecer alertas de eventos para serviços não-Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que a Event Grid suporta, consulte [Uma introdução à Grelha de Eventos Azure](https://docs.microsoft.com/azure/event-grid/overview).


![Modelo funcional da Grelha de Eventos Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos do Azure Maps

A grelha do evento utiliza subscrições de [eventos](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) para direcionar mensagens de eventos para assinantes. Uma conta Azure Maps emite os seguintes tipos de eventos: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Levantadas quando as coordenadas recebidas mudaram de fora de uma dada geoveda para dentro |
| Microsoft.Maps.GeofenceExited | Levantadas quando recebidas coordenadas mudaram de dentro de uma dada geocerca para fora |
| Microsoft.Maps.GeofenceResult | Levantada cada vez que uma consulta de geofencing devolve um resultado, independentemente do estado |

## <a name="event-schema"></a>Esquema de eventos

O exemplo que se segue mostra o esquema para geofenceResult:

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
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
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

## <a name="tips-for-consuming-events"></a>Dicas para consumir eventos

As aplicações que lidam com eventos geovetos do Azure Maps devem seguir algumas práticas recomendadas:

* Configure várias subscrições para direcionar eventos para o mesmo manipulador de eventos. É importante não assumir que os eventos são de uma fonte em particular. Verifique sempre o tópico da mensagem para garantir que a mensagem veio da fonte que espera.
* Utilize `X-Correlation-id` o campo no cabeçalho de resposta para perceber se a sua informação sobre objetos está atualizada. As mensagens podem chegar fora de ordem ou depois de um atraso.
* Quando um pedido GET ou POST na API Geofence é `EnterAndExit`chamado com o parâmetro de modo definido para , então um evento de entrada ou saída é gerado para cada geometria na geovete para a qual o estado mudou em relação à chamada anterior da Geofence API.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar geofencing para controlar operações num estaleiro de construção, consulte:

> [!div class="nextstepaction"] 
> [Criar uma geoveda através do Azure Maps](tutorial-geofence.md)

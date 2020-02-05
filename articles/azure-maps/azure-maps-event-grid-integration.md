---
title: Reagir a eventos de mapeamento usando a grade de eventos | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a reagir a Microsoft Azure eventos de mapas usando a grade de eventos.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a89983a9ae45f21deb7a823de049373b4ff9b935
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989064"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagir aos eventos do Azure Maps usando a grade de eventos 

O Azure Maps integra-se com a Azure Event Grid, para que os utilizadores possam enviar notificações de eventos para outros serviços e desencadear processos a jusante. O objetivo deste artigo é ajudá-lo a configurar as suas aplicações de negócio para ouvir eventos do Azure Maps. Este serviço permite-lhe reagir a eventos críticos de forma fiável, escalável e segura. Por exemplo, os utilizadores podem construir uma aplicação para atualizar uma base de dados, criar um bilhete e entregar uma notificação por e-mail, sempre que um dispositivo entra numa geovete.

A Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido, que utiliza um modelo de subscrição de publicações. A Event Grid tem suporte integrado para serviços Azure como [Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) e [Aplicações Lógicas Azure.](https://docs.microsoft.com/azure/azure-functions/functions-overview) Pode fornecer alertas de eventos para serviços não-Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos aos quais a grade de eventos dá suporte, consulte [uma introdução à grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).


![Modelo funcional da grade de eventos do Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos do Azure Maps

A grade de eventos usa [assinaturas de evento](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) para rotear mensagens de evento para assinantes. Uma conta do Azure Maps emite os seguintes tipos de evento: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Gerado quando as coordenadas recebidas foram movidas de fora de uma determinada cerca geográfica para dentro |
| Microsoft.Maps.GeofenceExited | Gerado quando as coordenadas recebidas foram movidas de dentro de uma determinada cerca geográfica para externa |
| Microsoft.Maps.GeofenceResult | Gerado toda vez que uma consulta de isolamento geográfica retorna um resultado, independentemente do estado |

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

Os aplicativos que lidam com os eventos de limite geográfico do Azure devem seguir algumas práticas recomendadas:

* Várias assinaturas podem ser configuradas para rotear eventos para o mesmo manipulador de eventos. É importante não supor que os eventos sejam de uma fonte específica. Sempre verifique o tópico da mensagem para garantir que ele venha da origem esperada.
* As mensagens podem chegar fora de ordem ou após um atraso. Utilize o campo `X-Correlation-id` no cabeçalho de resposta para perceber se a sua informação sobre objetos está atualizada.
* Quando a API Get e POST de cerca geográfica é chamada com o parâmetro mode definido como `EnterAndExit`, um evento Enter ou Exit é gerado para cada geometria no limite geográfico para o qual o status foi alterado da chamada à API de limite geográfico anterior.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o isolamento geográfico para controlar as operações em um site de construção, consulte:

> [!div class="nextstepaction"] 
> [Configurar uma cerca geográfica usando mapas do Azure](tutorial-geofence.md)

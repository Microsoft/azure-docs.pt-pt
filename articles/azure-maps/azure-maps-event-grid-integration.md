---
title: Reagir a eventos do Azure Maps com o Event Grid | Documentos da Microsoft
description: Saiba como reagir a eventos de mapas do Azure com o Event Grid.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799184"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagir a eventos do Azure Maps com o Event Grid 

Mapas do Azure integra-se com o Azure Event Grid para que possa enviar notificações de eventos para outros serviços e processos de downstream do acionador. O objetivo deste artigo é ajudar a configurar seus aplicativos de negócios para escutar eventos do Azure Maps para que possa reagir a eventos críticos de forma fiável, dimensionável e segura. Por exemplo, crie um aplicativo que realiza várias ações como atualizar uma base de dados, criar um pedido e fornecimento de uma notificação por e-mail sempre que um dispositivo é inserido um perímetro geográfico.

O Azure Event Grid é um serviço de encaminhamento de eventos totalmente gerido que utiliza um publicar-subscrever o modelo. Grelha de eventos possui suporte interno para serviços do Azure como [as funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) e [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)e pode enviar alertas de eventos para serviços não pertencente ao Azure através de webhooks. Para obter uma lista completa dos manipuladores de eventos que suporta o Event Grid, veja [uma introdução ao Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Modelo de funcional do Azure Event Grid](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos do Azure Maps

Grelha de eventos usa [subscrições de eventos](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) encaminhar mensagens de eventos para os assinantes. Uma conta do Azure Maps emite os seguintes tipos de evento: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Gerado quando recebidas de coordenadas passaram de fora de um determinado perímetro geográfico para dentro |
| Microsoft.Maps.GeofenceExited | Gerado quando recebidas de coordenadas passaram de dentro de um determinado perímetro geográfico para fora |
| Microsoft.Maps.GeofenceResult | Gerado sempre que uma consulta de barreira geográfica devolve um resultado, independentemente do Estado |

## <a name="event-schema"></a>Esquema de eventos

O esquema de apresentação do exemplo seguinte para GeofenceResult

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

## <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Aplicações que processam os eventos do Azure Maps perímetro geográfico devem seguir algumas práticas recomendadas:

* Várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos. É importante não partem do princípio de que os eventos são de uma origem específica. Verifique sempre o tópico de mensagem para se certificar de que trata da origem de que pretende.
* As mensagens podem chegar fora de ordem ou após um atraso. Utilize o `X-Correlation-id` campo no cabeçalho de resposta para saber se as informações sobre objetos são atualizadas.
* Quando começar e a API do perímetro geográfico de publicação é chamado com o parâmetro de modo definido como `EnterAndExit`, um evento de Enter ou Exit é gerado para cada geometry no perímetro geográfico para o qual o estado mudou da chamada de API do perímetro geográfico anterior.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como utilizar a barreira geográfica para operações de controle num site de construção, veja:

> [!div class="nextstepaction"] 
> [Configure um perímetro geográfico, utilizando o Azure Maps](tutorial-geofence.md)
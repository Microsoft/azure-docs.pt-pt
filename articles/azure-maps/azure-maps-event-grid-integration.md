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
ms.openlocfilehash: 9a946d189706c9c789ab884670d13b0b3e7fcb0c
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911801"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagir aos eventos do Azure Maps usando a grade de eventos 

O Azure Maps integra-se à grade de eventos do Azure para que você possa enviar notificações de eventos para outros serviços e disparar processos downstream. A finalidade deste artigo é ajudá-lo a configurar seus aplicativos de negócios para escutar eventos do Azure Maps para que você possa reagir a eventos críticos de maneira confiável, escalonável e segura. Por exemplo, crie um aplicativo que executa várias ações como atualizar um banco de dados, criar um tíquete e entregar uma notificação por email toda vez que um dispositivo entrar em uma cerca geográfica.

A grade de eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. A grade de eventos tem suporte interno para serviços do Azure, como [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) e [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview), e pode entregar alertas de eventos a serviços não Azure usando WebHooks. Para obter uma lista completa dos manipuladores de eventos aos quais a grade de eventos dá suporte, consulte [uma introdução à grade de eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).


![Modelo funcional da grade de eventos do Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos do Azure Maps

A grade de eventos usa [assinaturas de evento](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) para rotear mensagens de evento para assinantes. Uma conta do Azure Maps emite os seguintes tipos de evento: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Gerado quando as coordenadas recebidas foram movidas de fora de uma determinada cerca geográfica para dentro |
| Microsoft.Maps.GeofenceExited | Gerado quando as coordenadas recebidas foram movidas de dentro de uma determinada cerca geográfica para externa |
| Microsoft.Maps.GeofenceResult | Gerado toda vez que uma consulta de isolamento geográfica retorna um resultado, independentemente do estado |

## <a name="event-schema"></a>Esquema de eventos

O exemplo a seguir mostra o esquema para GeofenceResult

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
* As mensagens podem chegar fora de ordem ou após um atraso. Use o campo `X-Correlation-id` no cabeçalho de resposta para entender se suas informações sobre objetos estão atualizadas.
* Quando a API Get e POST de cerca geográfica é chamada com o parâmetro mode definido como `EnterAndExit`, um evento Enter ou Exit é gerado para cada geometria no limite geográfico para o qual o status foi alterado da chamada à API de limite geográfico anterior.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o isolamento geográfico para controlar as operações em um site de construção, consulte:

> [!div class="nextstepaction"] 
> [Configurar uma cerca geográfica usando mapas do Azure](tutorial-geofence.md)
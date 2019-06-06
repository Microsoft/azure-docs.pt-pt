---
title: Esquema de eventos do Azure Event Grid Azure Maps
description: Descreve as propriedades e o esquema fornecido para eventos de mapas do Azure com o Azure Event Grid
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861859"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Esquema de eventos do Azure Event Grid para o Azure Maps

Este artigo fornece as propriedades e o esquema para eventos do Azure Maps. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

Uma conta do Azure Maps emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Gerado quando recebidas de coordenadas passaram de fora de um determinado perímetro geográfico para dentro |
| Microsoft.Maps.GeofenceExited | Gerado quando recebidas de coordenadas passaram de dentro de um determinado perímetro geográfico para fora |
| Microsoft.Maps.GeofenceResult | Gerado sempre que uma consulta de barreira geográfica devolve um resultado, independentemente do Estado |

## <a name="event-examples"></a>Exemplos de evento

O exemplo seguinte mostra o esquema de um **GeofenceEntered** eventos

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

O exemplo seguinte mostra o esquema para **GeofenceResult** 

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

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Event Grid fornece este valor. |
| subject | string | Caminho definidos pelo publicador para o assunto de evento. |
| eventType | string | Um dos tipos de eventos registrados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| id | string | Identificador exclusivo para o evento. |
| data | objeto | Dados de eventos de barreira geográfica. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| apiCategory | string | Categoria de API do evento. |
| apiName | string | Nome da API do evento. |
| issues | objeto | Lista problemas durante o processamento. Se todos os problemas são devolvidos, em seguida, não haverá nenhum geometrias devolvidas com a resposta. |
| responseCode | número | Código de resposta HTTP |
| geometries | objeto | Listas de geometrias cerca que contêm a coordenada posicionam ou se sobrepõem searchBuffer em torno da posição. |

O objeto de erro é retornado quando ocorre um erro na API de mapas. O objeto de erro tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| error | ErrorDetails |Este objeto é retornado quando ocorre um erro na API de mapas  |

O objeto de ErrorDetails é retornado quando ocorre um erro na API de mapas. O ErrorDetails ou o objeto tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| code | string | O código de estado HTTP. |
| message | string | Se estiver disponível, uma descrição legível humana do erro. |
| innererror | InnerError | Se estiver disponível, um objeto que contém informações de serviços específicos sobre o erro. |

O InnerError é um objeto que contém informações de serviços específicos sobre o erro. O objeto de InnerError tem as seguintes propriedades: 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| code | string | A mensagem de erro. |

O objeto de geometrias, apresenta uma lista de IDs dos perímetros geográficos que expiraram em relação ao tempo de utilizador no pedido de geometria. O objeto de geometrias tem itens de geometria com as seguintes propriedades: 

| Propriedade | Tipo | Descrição |
|:-------- |:---- |:----------- |
| deviceid | string | ID do dispositivo. |
| distance | string | <p>Distância da coordenada até o limite mais próximo do perímetro geográfico. Positivo significa que a coordenada está fora do perímetro geográfico. Se a coordenada está fora do perímetro geográfico, mas mais do que o valor de searchBuffer para fora do limite mais próximo do perímetro geográfico, em seguida, o valor é 999. Negativo significa a coordenada dentro do perímetro geográfico. Se a coordenada é dentro do polígono, mas mais do que o valor de searchBuffer para fora do limite de barreira geográfica mais próximo, em seguida, o valor é -999. Um valor de 999 significa que há muita confiança a coordenada é bem fora do perímetro geográfico. Um valor de meios de-999 que não há muita confiança a coordenada é bem dentro do perímetro geográfico.<p> |
| geometryid |string | O id exclusivo identifica a geometria de perímetro geográfico. |
| nearestlat | número | Latitude do ponto mais próximo da geometria. |
| nearestlon | número | Longitude do ponto mais próximo da geometria. |
| udId | string | O id exclusivo retornado do serviço de carregamento do utilizador ao carregar um perímetro geográfico. Não será incluído na API de publicação de barreira geográfica. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Listas do ID de geometria do perímetro geográfico que expirou em relação ao tempo de utilizador no pedido. |
| geometries | geometries[] |Listas de geometrias cerca que contêm a coordenada posicionam ou se sobrepõem searchBuffer em torno da posição. |
| invalidPeriodGeofenceGeometryId | string[]  | Listas do ID de geometria do perímetro geográfico que está no período inválido em relação ao tempo de utilizador no pedido. |
| isEventPublished | boolean | VERDADEIRO se pelo menos um evento é publicado para o subscritor de eventos do Azure Maps, FALSO se nenhum evento é publicado para o subscritor de eventos do Azure Maps. |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).

---
title: Azure Maps como fonte de grade de eventos
description: Descreve as propriedades e esquemas fornecidos para eventos Azure Maps com Azure Event Grid
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: femila
ms.openlocfilehash: f015bf682d7ce3475aba5baa73ab72b1426691fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560679"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Azure Maps como fonte de grade de eventos

Este artigo fornece as propriedades e esquema para eventos Azure Maps. Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema). Também lhe dá uma lista de partidas rápidas e tutoriais para usar o Azure Maps como fonte de evento.

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

Uma conta Azure Maps emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Levantado quando as coordenadas recebidas mudaram de fora de uma dada geofência para dentro |
| Microsoft.Maps.GeofenceExited | Levantado quando as coordenadas recebidas mudaram de dentro de uma dada geofência para fora |
| Microsoft.Maps.GeofenceResult | Levantado cada vez que uma consulta geofencing devolve um resultado, independentemente do estado |

### <a name="event-examples"></a>Exemplos de eventos

O exemplo a seguir mostra o esquema de um evento **GeofenceEntered**

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

O exemplo a seguir mostra esquema para **GeofenceResult** 

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

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| dados | objeto | Dados de eventos geofencing. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| apiCategoria | string | Categoria API do evento. |
| apiName | string | Nome da API do evento. |
| issues | objeto | Lista as questões encontradas durante o processamento. Se algum problema for devolvido, então não haverá geometrias devolvidas com a resposta. |
| responseCode | número | Código de resposta HTTP |
| geometrias | objeto | Lista as geometrias da cerca que contêm a posição da coordenada ou sobrepõem o searchBuffer em torno da posição. |

O objeto de erro é devolvido quando ocorre um erro na API do Mapa. O objeto de erro tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| erro | ErrorDetails |Este objeto é devolvido quando ocorre um erro na API do Mapa  |

O objeto ErrorDetails é devolvido quando ocorre um erro na API do Mapa. O ErrorDetails ou o objeto tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| code | string | O código de estado HTTP. |
| message | string | Se disponível, uma descrição legível humana do erro. |
| interior | InteriorError | Se disponível, um objeto que contenha informações específicas do serviço sobre o erro. |

O InnerError é um objeto que contém informações específicas do serviço sobre o erro. O objeto InnerError tem as seguintes propriedades: 

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| code | string | A mensagem de erro. |

O objeto de geometria, lista iDs de geometria das geosfências que expiraram em relação ao tempo de utilização no pedido. O objeto de geometria tem itens de geometria com as seguintes propriedades: 

| Propriedade | Tipo | Description |
|:-------- |:---- |:----------- |
| deviceid | string | Identificação do dispositivo. |
| distância | string | <p>Distância da coordenada até à fronteira mais próxima da geofence. Positivo significa que a coordenada está fora da geofência. Se a coordenada estiver fora da geofence, mas mais do que o valor de searchBuffer longe da fronteira de geofência mais próxima, então o valor é 999. Negativo significa que a coordenada está dentro da geofência. Se a coordenada estiver dentro do polígono, mas mais do que o valor de searchBuffer longe da fronteira geofencing mais próxima, então o valor é -999. Um valor de 999 significa que há uma grande confiança que a coordenada está bem fora da geo-fence. Um valor de -999 significa que há uma grande confiança que a coordenada está bem dentro da geofência.<p> |
| geometria |string | O id único identifica a geometria da geofência. |
| nearestlat | número | Latitude do ponto mais próximo da geometria. |
| nearestlon | número | Longitude do ponto mais próximo da geometria. |
| udId | string | O id único devolvido do serviço de upload do utilizador ao carregar um geofence. Não será incluída na API de geofencing pós-api. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | corda[] | Listas do ID de geometria da geofência que é expirada em relação ao tempo de utilizador no pedido. |
| geometrias | geometrias[] |Lista as geometrias da cerca que contêm a posição da coordenada ou sobrepõem o searchBuffer em torno da posição. |
| invalidPeriodGeofenceGeometryId | corda[]  | Listas do ID de geometria da geofência que se encontra em período inválido em relação ao tempo de utilização no pedido. |
| isEvent Publicado | boolean | É verdade que se pelo menos um evento for publicado no assinante do evento Azure Maps, falso se nenhum evento for publicado no assinante do evento Azure Maps. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título  |Descrição  |
|---------|---------|
| [Reagir aos eventos do Azure Maps utilizando a Grade de Eventos](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração dos Mapas Azure com a Grade de Eventos. |
| [Tutorial: Criar uma geofence](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Este tutorial acompanha-o através dos passos básicos para configurar a geofence usando o Azure Maps. Utiliza a Grelha de Eventos Azure para transmitir os resultados da geofência e configura uma notificação com base nos resultados da geofence. |

## <a name="next-steps"></a>Próximos passos

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
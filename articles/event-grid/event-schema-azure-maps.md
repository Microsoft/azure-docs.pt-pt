---
title: Esquema de evento azure do evento Azure Maps do Azure Maps
description: Descreve as propriedades e esquemas fornecidos para eventos do Azure Maps com a Grelha de Eventos Azure
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: femila
ms.openlocfilehash: 9acef524521e8fac6ce6f8f61e5ff3fbbb81d18d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486364"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Esquema de evento sinuoso do evento Azure Event Grid para o Azure Maps

Este artigo fornece as propriedades e esquemas para eventos do Azure Maps. Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Uma conta Azure Maps emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Levantados quando as coordenadas recebidas mudaram de fora de uma dada geoveda para dentro |
| Microsoft.Maps.GeofenceExited | Levantadas quando as coordenadas recebidas se deslocaram de dentro de uma dada geoveda para fora |
| Microsoft.Maps.GeofenceResult | Levantada cada vez que uma consulta de geofencing devolve um resultado, independentemente do estado |

## <a name="event-examples"></a>Exemplos de eventos

O exemplo que se segue mostra o esquema de um evento **GeofenceEntered**

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

O exemplo seguinte mostra esquema para **GeofenceResult** 

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

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. A Grelha de Eventos fornece este valor. |
| subject | string | Caminho definido pela editora para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta fonte do evento. |
| eventoTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento geofencing. |
| dataVersion | string | A versão esquema do objeto de dados. A editora define a versão do esquema. |
| metadataVersion | string | A versão de esquema dos metadados do evento. A Grelha de Eventos define o esquema das propriedades de alto nível. A Grelha de Eventos fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| apiCategoria | string | Categoria API do evento. |
| apiName | string | Nome da API do evento. |
| issues | objeto | Lista os problemas encontrados durante o processamento. Se algum problema for devolvido, então não haverá geometrias devolvidas com a resposta. |
| responseCode | número | Código de resposta HTTP |
| geometries | objeto | Enumera as geometrias da vedação que contêm a posição de coordenada ou sobrepõem o tampão de busca em torno da posição. |

O objeto de erro é devolvido quando ocorre um erro na API do Maps. O objeto de erro tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| erro | ErrorDetails |Este objeto é devolvido quando ocorre um erro na API do Maps  |

O objeto ErrorDetails é devolvido quando ocorre um erro na API do Maps. Os ErrorDetails ou objeto tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| code | string | O código de estado HTTP. |
| message | string | Se disponível, uma descrição legível humana do erro. |
| erro interno | InnerError | Se disponível, um objeto que contenha informações específicas do serviço sobre o erro. |

O InnerError é um objeto que contém informações específicas do serviço sobre o erro. O objeto InnerError tem as seguintes propriedades: 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| code | string | A mensagem de erro. |

O objeto de geometria, lista iDs de geometria das geovedações que expiraram em relação ao tempo de utilizador no pedido. O objeto de geometria tem itens de geometria com as seguintes propriedades: 

| Propriedade | Tipo | Descrição |
|:-------- |:---- |:----------- |
| deviceid | string | Identificação do dispositivo. |
| distância | string | <p>Distância da coordenada até à fronteira mais próxima da geoveda. Positivo significa que a coordenada está fora da geocerca. Se a coordenada estiver fora da geoveda, mas mais do que o valor da pesquisaBuffer longe da fronteira geovete mais próxima, então o valor é 999. Negativo significa que a coordenada está dentro da geocerca. Se a coordenada estiver dentro do polígono, mas mais do que o valor da pesquisaBuffer longe da fronteira geofencing mais próxima, então o valor é -999. Um valor de 999 significa que há grande confiança que a coordenada está bem fora da geocerca. Um valor de -999 significa que há grande confiança que a coordenada está bem dentro da geoveda.<p> |
| geometride |string | A identificação única identifica a geometria geoveda. |
| mais próximo | número | Latitude do ponto mais próximo da geometria. |
| mais próximo | número | Longitude do ponto mais próximo da geometria. |
| udId | string | O id único devolvido do serviço de upload do utilizador ao carregar uma geovese. Não será incluído no posto de geofencing post API. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Listas da identificação de geometria da geovete que é expirada em relação ao tempo de utilizador no pedido. |
| geometries | geometrias[] |Enumera as geometrias da vedação que contêm a posição de coordenada ou sobrepõem o tampão de busca em torno da posição. |
| invalidPeriodGeofenceGeometryId | string[]  | Listas da identificação de geometria da geoveda que se encontra em período inválido em relação ao tempo de utilização no pedido. |
| isEventPublished | boolean | É verdade que pelo menos um evento é publicado para o assinante do evento Azure Maps, falso se nenhum evento for publicado para o subscritor do evento Azure Maps. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
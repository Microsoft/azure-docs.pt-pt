---
title: Mapas Azure como fonte da Grelha de Eventos
description: Descreve as propriedades e esquemas fornecidos para eventos do Azure Maps com a Grelha de Eventos Azure
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: e879ec3442f2e7912acb450a97079d80d7d95a01
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393410"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Azure Maps como fonte da Grelha de Eventos

Este artigo fornece as propriedades e esquemas para eventos do Azure Maps. Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](https://docs.microsoft.com/azure/event-grid/event-schema). Também lhe dá uma lista de arranques rápidos e tutoriais para usar o Azure Maps como fonte de evento.

## <a name="event-grid-event-schema"></a>Esquema de evento sinuoso do evento Da Grelha de Eventos

### <a name="available-event-types"></a>Tipos de eventos disponíveis

Uma conta Azure Maps emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Levantados quando as coordenadas recebidas mudaram de fora de uma dada geoveda para dentro |
| Microsoft.Maps.GeofenceExited | Levantadas quando as coordenadas recebidas se deslocaram de dentro de uma dada geoveda para fora |
| Microsoft.Maps.GeofenceResult | Levantada cada vez que uma consulta de geofencing devolve um resultado, independentemente do estado |

### <a name="event-examples"></a>Exemplos de eventos

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

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento geofencing. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| apiCategoria | string | Categoria API do evento. |
| apiName | string | Nome da API do evento. |
| issues | objeto | Lista os problemas encontrados durante o processamento. Se algum problema for devolvido, então não haverá geometrias devolvidas com a resposta. |
| responseCode | número | Código de resposta HTTP |
| geometrias | objeto | Enumera as geometrias da vedação que contêm a posição de coordenada ou sobrepõem o tampão de busca em torno da posição. |

O objeto de erro é devolvido quando ocorre um erro na API do Maps. O objeto de erro tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| erro | ErrorDetails |Este objeto é devolvido quando ocorre um erro na API do Maps  |

O objeto ErrorDetails é devolvido quando ocorre um erro na API do Maps. Os ErrorDetails ou objeto tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| code | string | O código de estado HTTP. |
| message | string | Se disponível, uma descrição legível humana do erro. |
| erro interno | Erro Interior | Se disponível, um objeto que contenha informações específicas do serviço sobre o erro. |

O InnerError é um objeto que contém informações específicas do serviço sobre o erro. O objeto InnerError tem as seguintes propriedades: 

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| code | string | A mensagem de erro. |

O objeto de geometria, lista iDs de geometria das geovedações que expiraram em relação ao tempo de utilizador no pedido. O objeto de geometria tem itens de geometria com as seguintes propriedades: 

| Propriedade | Tipo | Descrição |
|:-------- |:---- |:----------- |
| dispositivo | string | Identificação do dispositivo. |
| distância | string | <p>Distância da coordenada até à fronteira mais próxima da geoveda. Positivo significa que a coordenada está fora da geocerca. Se a coordenada estiver fora da geoveda, mas mais do que o valor da pesquisaBuffer longe da fronteira geovete mais próxima, então o valor é 999. Negativo significa que a coordenada está dentro da geocerca. Se a coordenada estiver dentro do polígono, mas mais do que o valor da pesquisaBuffer longe da fronteira geofencing mais próxima, então o valor é -999. Um valor de 999 significa que há grande confiança que a coordenada está bem fora da geocerca. Um valor de -999 significa que há grande confiança que a coordenada está bem dentro da geoveda.<p> |
| geometride |string | A identificação única identifica a geometria geoveda. |
| mais próximo | número | Latitude do ponto mais próximo da geometria. |
| mais próximo | número | Longitude do ponto mais próximo da geometria. |
| udId | string | O id único devolvido do serviço de upload do utilizador ao carregar uma geovese. Não será incluído no posto de geofencing post API. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| expiradoGeofenceGeometryId | cadeia[] | Listas da identificação de geometria da geovete que é expirada em relação ao tempo de utilizador no pedido. |
| geometrias | geometrias[] |Enumera as geometrias da vedação que contêm a posição de coordenada ou sobrepõem o tampão de busca em torno da posição. |
| inválidoPeriodGeofenceGeometryId | cadeia[]  | Listas da identificação de geometria da geoveda que se encontra em período inválido em relação ao tempo de utilização no pedido. |
| isEventPublished | boolean | É verdade que pelo menos um evento é publicado para o assinante do evento Azure Maps, falso se nenhum evento for publicado para o subscritor do evento Azure Maps. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título  |Descrição  |
|---------|---------|
| [Reaja aos eventos do Azure Maps usando a Grelha de Eventos](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Visão geral da integração do Azure Maps com a Grelha de Eventos. |
| [Tutorial: Criar uma geovete](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Este tutorial acompanha-o através dos passos básicos para configurar geovese usando o Azure Maps. Utilize a Rede de Eventos Azure para transmitir os resultados da geovedação e configurar uma notificação com base nos resultados da geovedação. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
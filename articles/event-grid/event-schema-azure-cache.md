---
title: Cache Azure para Redis como fonte de Grade de Eventos
description: Descreve as propriedades que são fornecidas para Azure Cache para eventos Redis com Azure Event Grid
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056221"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Cache Azure para Redis como fonte de grade de eventos

Este artigo fornece as propriedades e esquema para Azure Cache para eventos Redis. Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md). 

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Lista de eventos para Azure Cache para APIs do Redis REST

Estes eventos são desencadeados quando um cliente exporta, importa ou escala, chamando Azure Cache para Redis REST APIs. O evento de remendação é desencadeado pela atualização do Redis.

 |Nome do evento |Description|
 |----------|-----------|
 |**Microsoft.cache.exportRDBCompleted** |Desencadeado quando os dados da cache são exportados. |
 |**Microsoft.Cache.ImportRDBCompleted** |Desencadeado quando os dados da cache são importados. |
 |**Microsoft.Cache.PatchingCompleted** |Acionado quando o remendos estiver concluído. |
 |**Microsoft.Cache.ScalingCompleted** |Desencadeado quando o escalonamento estiver concluído. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta do evento

Quando um evento é desencadeado, o serviço De Grelha de Eventos envia dados sobre esse evento para subscrever o ponto final.

Esta secção contém um exemplo de como seriam esses dados para cada Azure Cache para o evento Redis.

### <a name="microsoftcachepatchingcompleted-event"></a>Microsoft.Cache.PatchingCompleted event

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Microsoft.Cache.ImportRDBCompleted event

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Microsoft.Cache.ExportRDBCompleted event

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft.Cache.ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| dados | objeto | Azure Cache para os dados do evento Redis. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Description |
| -------- | ---- | ----------- |
| carimbo de data/hora | string | A hora em que o evento ocorreu. |
| name | string | O nome do evento. |
| status | string | O estado do evento. Falhou ou conseguiu. |


## <a name="quickstarts"></a>Guias de Início Rápido

Se quiser experimentar Azure Cache para eventos Redis, consulte qualquer um destes artigos de arranque rápido:

|Se quiser utilizar esta ferramenta:    |Consulte este artigo: |
|--|-|
|Portal do Azure    |[Quickstart: Route Azure Cache para eventos Redis para o ponto final da web com o portal Azure](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Quickstart: Route Azure Cache para eventos redis para o ponto final da web com PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|CLI do Azure    |[Quickstart: Route Azure Cache para eventos Redis para o ponto final da web com Azure CLI](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).


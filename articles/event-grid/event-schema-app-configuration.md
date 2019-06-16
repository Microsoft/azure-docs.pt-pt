---
title: Esquema de eventos do Azure Event Grid configuração de aplicações do Azure
description: Descreve as propriedades que são fornecidas para eventos de configuração de aplicações do Azure com o Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735785"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Esquema de eventos do Azure Event Grid para configuração de aplicações do Azure

Este artigo fornece as propriedades e o esquema para eventos de configuração de aplicações do Azure. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](event-schema.md).

Para obter uma lista de scripts de exemplo e tutoriais, consulte [origem de eventos de configuração de aplicações do Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

Configuração de aplicações do Azure emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Desencadeado quando um valor de chave é criado ou substituído. |
| Microsoft.AppConfiguration.KeyValueDeleted | Desencadeado quando um valor de chave é eliminado. |

## <a name="example-event"></a>Evento de exemplo

O exemplo seguinte mostra o esquema de um evento de modificação de chave-valor: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

O esquema para um evento eliminado de chave-valor é semelhante: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
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
| data | objeto | Dados de eventos de configuração de aplicação. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| key | string | A chave de chave-valor que foi alterada ou eliminada. |
| label | string | A etiqueta, caso haja algum, de chave-valor que foi alterado ou eliminado. |
| etag | string | Para `KeyValueModified` a etag do novo valor de chave. Para `KeyValueDeleted` a etag de chave-valor que foi eliminada. |
 
## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
* Para uma introdução ao trabalhar com eventos de configuração de aplicações do Azure, consulte [eventos de configuração de aplicações do Azure de rotas - CLI do Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
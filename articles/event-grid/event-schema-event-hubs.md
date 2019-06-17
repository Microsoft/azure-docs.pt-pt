---
title: Esquema do evento de hubs de eventos do Azure Event Grid
description: Descreve as propriedades que são fornecidas para eventos de hubs de eventos com o Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60561833"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Esquema de eventos do Azure Event Grid para os hubs de eventos

Este artigo fornece as propriedades e o esquema para eventos de hubs de eventos. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](event-schema.md).

Para obter uma lista de scripts de exemplo e tutoriais, consulte [origem de eventos de Hubs de eventos](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Tipos de eventos disponíveis

Os Hubs de eventos emite a **Microsoft.EventHub.CaptureFileCreated** tipo de evento quando é criado um ficheiro de captura.

## <a name="example-event"></a>Evento de exemplo

Este evento de exemplo mostra o esquema de um evento de hubs de eventos gerado quando a funcionalidade de captura armazena um arquivo: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
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
| data | objeto | Dados de eventos do hub de eventos. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| fileUrl | string | O caminho para o arquivo de captura. |
| fileType | string | O tipo de ficheiro de arquivo de captura. |
| partitionId | string | O ID de partição horizontal. |
| sizeInBytes | inteiro | O tamanho do ficheiro. |
| eventCount | inteiro | O número de eventos no arquivo. |
| firstSequenceNumber | inteiro | O menor número de sequência da fila. |
| lastSequenceNumber | inteiro | O último número de sequência da fila. |
| firstEnqueueTime | string | Na primeira vez da fila. |
| lastEnqueueTime | string | A última vez da fila. |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
* Para obter informações sobre o processamento de eventos de hubs de eventos, consulte [Stream grandes quantidades de dados para um armazém de dados](event-grid-event-hubs-integration.md).
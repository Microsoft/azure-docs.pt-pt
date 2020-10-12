---
title: Hubs de eventos Azure como fonte de grade de eventos
description: Descreve as propriedades que são fornecidas para eventos de centros de eventos com Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 960aa1fe7184e1d02d28fdc135907119fee8f123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86113688"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Azure Event Hubs como fonte de grade de eventos

Este artigo fornece as propriedades e esquema para eventos de centros de eventos.Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md).

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

O Event Hubs emite o tipo de evento **Microsoft.EventHub.CaptureFileCreated** quando um ficheiro de captura é criado.

### <a name="example-event"></a>Exemplo evento

Este evento de amostra mostra o esquema de um evento de centros de eventos criado quando a funcionalidade de captura armazena um ficheiro: 

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

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| dados | objeto | Dados do evento hub event. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| fileUrl | string | O caminho para o ficheiro de captura. |
| fileType | string | O tipo de ficheiro do ficheiro de captura. |
| partitionId | string | A identificação do fragmento. |
| tamanhoInBytes | número inteiro | O tamanho do ficheiro. |
| eventosoconta | número inteiro | O número de eventos no arquivo. |
| firstSequenceNumber | número inteiro | O menor número de sequência da fila. |
| lastSequenceNumber | número inteiro | O último número de sequência da fila. |
| firstEnqueueTime | string | A primeira vez da fila. |
| lastEnqueueTime | string | A última vez da fila. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)

|Título  |Descrição  |
|---------|---------|
| [Tutorial: transmitir big data para um armazém de dados](event-grid-event-hubs-integration.md) | Quando o Event Hubs cria um ficheiro Capture, o Event Grid envia um evento para uma aplicação de função. A aplicação recupera o ficheiro Captura e migra dados para um armazém de dados. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
* Para obter informações sobre como lidar com eventos de centros de eventos, consulte [o Stream big data num armazém de dados.](event-grid-event-hubs-integration.md)
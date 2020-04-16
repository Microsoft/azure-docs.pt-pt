---
title: Hubs de eventos azure como fonte da Grelha de Eventos
description: Descreve as propriedades que estão previstas para eventos de hubs de eventos com a Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fd65c20f07a091fa1fc8a6cbf003986e1096ebe3
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393344"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Hubs de eventos azure como fonte da Grelha de Eventos

Este artigo fornece as propriedades e esquemas para eventos de hubs de eventos.Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Esquema de evento sinuoso do evento Da Grelha de Eventos

### <a name="available-event-types"></a>Tipos de eventos disponíveis

Os Hubs de Eventos emitem o tipo de evento **Microsoft.EventHub.CaptureFileCreated** quando um ficheiro de captura é criado.

### <a name="example-event"></a>Evento de exemplo

Este evento de amostra mostra o esquema de um evento de hubs de eventos criado quando a funcionalidade de captura armazena um ficheiro: 

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
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento hub. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| fileUrl | string | O caminho para o ficheiro de captura. |
| arquivoType | string | O tipo de ficheiro do ficheiro de captura. |
| partiçãoId | string | A identificação do caco. |
| tamanhoInBytes | número inteiro | O tamanho do ficheiro. |
| eventoCount | número inteiro | O número de eventos no ficheiro. |
| primeiroSequenceNumber | número inteiro | O menor número de sequência da fila. |
| últimoSequenceNumber | número inteiro | O último número de sequência da fila. |
| firstEnqueueTime | string | A primeira vez da fila. |
| lastEnqueueTime | string | A última vez da fila. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)

|Título  |Descrição  |
|---------|---------|
| [Tutorial: transmitir big data em um armazém de dados](event-grid-event-hubs-integration.md) | Quando o Event Hubs cria um ficheiro Capture, a Rede de Eventos envia um evento para uma aplicação de função. A aplicação recupera o ficheiro Capture e migra dados para um armazém de dados. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
* Para obter informações sobre o tratamento de eventos de centros de eventos, consulte [o Stream big data num armazém de dados](event-grid-event-hubs-integration.md).
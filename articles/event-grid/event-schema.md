---
title: Esquema de eventos do Azure Event Grid
description: Descreve as propriedades que são fornecidas para eventos com o Azure Event Grid
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/20/2019
ms.author: babanisa
ms.openlocfilehash: 4a795221790a9d56bcbfe30a50b0c838fb8d9e56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304238"
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos do Azure Event Grid

Este artigo descreve as propriedades e o esquema que estão presentes para todos os eventos. Os eventos consistem num conjunto de propriedades de cadeia de caracteres necessárias cinco e um objeto de dados necessários. As propriedades são comuns a todos os eventos de qualquer fabricante. O objeto de dados tem propriedades que são específicas para cada editor. Para os tópicos de sistema, estas propriedades são específicas para o fornecedor de recursos, como o armazenamento do Azure ou Event Hubs do Azure.

Origens de eventos enviam eventos para o Azure Event Grid numa matriz, o que pode ter vários objetos de evento. Eventos para um tópico do event grid no lançamento, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz está limitado a 64 KB (disponibilidade geral) ou 1 MB (pré-visualização). Se um evento ou a matriz é maior do que os limites de tamanho, receber a resposta **413 Payload demasiado grande**.

> [!NOTE]
> Um evento de tamanho até 64 KB é abordado pelo contrato de nível de serviço do disponibilidade geral (GA) (SLA). O suporte para um evento de tamanho até 1 MB está atualmente em pré-visualização. Eventos mais de 64 KB são cobrados em incrementos de 64 KB. 

Grelha de eventos envia os eventos para os assinantes numa matriz com um único evento. Este comportamento poderá mudar no futuro.

Pode encontrar o esquema JSON para o evento do Event Grid e payload de dados de cada publicador do Azure no [arquivo do esquema de eventos](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Esquema de eventos

O exemplo seguinte mostra as propriedades que são utilizadas por todos os publicadores de eventos:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Por exemplo, o esquema publicado um evento de armazenamento de Blobs do Azure é:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Propriedades do evento

Todos os eventos possuem os mesmos dados de nível superior seguintes:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Event Grid fornece este valor. |
| subject | string | Caminho definidos pelo publicador para o assunto de evento. |
| eventType | string | Um dos tipos de eventos registrados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| id | string | Identificador exclusivo para o evento. |
| data | objeto | Dados de eventos específicos para o fornecedor de recursos. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

Para saber mais sobre as propriedades no objeto de dados, veja a origem do evento:

* [Subscrições do Azure (operações de gestão)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Armazenamento de blobs](event-schema-blob-storage.md)
* [Hubs de Eventos](event-schema-event-hubs.md)
* [Hub IoT](event-schema-iot-hub.md)
* [Serviços de Multimédia](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupos de recursos (operações de gestão)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)

Para obter tópicos personalizados, o publicador de eventos determina o objeto de dados. Os dados de nível superior devem ter os mesmos campos que eventos padrão definido pelo recurso.

Quando publica eventos tópicos personalizados, crie assuntos para seus eventos que tornam fácil para os assinantes para saber se elas têm interesse no evento. Os subscritores utilizam o assunto para filtrar e encaminhar eventos. Considere fornecer o caminho para onde o evento ter acontecido, para que os assinantes podem filtrar por segmentos de caminho. O caminho permite que os subscritores de foco menor ou amplamente filtrar eventos. Por exemplo, se fornecer um caminho de três segmento como `/A/B/C` no assunto, os assinantes podem filtrar pelo primeiro segmento `/A` para obter um amplo conjunto de eventos. Os subscritores recebem eventos com os assuntos como `/A/B/C` ou `/A/D/E`. Outros subscritores podem filtrar por `/A/B` para obter um conjunto mais estreito de eventos.

Às vezes, o requerente tem mais detalhes sobre o que aconteceu. Por exemplo, o **contas de armazenamento** publicador fornece o assunto `/blobServices/default/containers/<container-name>/blobs/<file>` quando um ficheiro é adicionado a um contentor. Um subscritor pode filtrar pelo caminho `/blobServices/default/containers/testcontainer` para obter todos os eventos para esse contentor, mas não outros contentores na conta de armazenamento. Um subscritor pode também filtrar ou rota pelo sufixo `.txt` apenas trabalhar com arquivos de texto.

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).

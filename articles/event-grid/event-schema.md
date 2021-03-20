---
title: Esquema de eventos Azure Event Grid
description: Descreve as propriedades e esquemas que estão presentes para todos os eventos. Os eventos consistem num conjunto de cinco propriedades de cordas necessárias e um objeto de dados necessário.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: 7ddc7c78c5a9e5ba2a57b21c45fb9fab65056ee9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86105885"
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos Azure Event Grid

Este artigo descreve as propriedades e esquemas que estão presentes para todos os eventos. Os eventos consistem num conjunto de cinco propriedades de cordas necessárias e um objeto de dados necessário. As propriedades são comuns a todos os eventos de qualquer editor. O objeto de dados tem propriedades específicas de cada editor. Para tópicos do sistema, estas propriedades são específicas para o fornecedor de recursos, como Azure Storage ou Azure Event Hubs.

Fontes do evento enviam eventos para a Azure Event Grid numa matriz, que pode ter vários objetos de evento. Ao publicar eventos num tópico de grelha de evento, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz é limitado a 1 MB. Se um evento ou a matriz for maior do que os limites de tamanho, receberá a resposta **413 Payload Too Large**. No entanto, as operações são cobradas em incrementos de 64 KB. Assim, eventos acima de 64 KB incorrerão em cargas de operações como se fossem múltiplos eventos. Por exemplo, um evento de 130 KB incorreria em operações como se fossem 3 eventos separados.

O Event Grid envia os eventos para os assinantes numa matriz que tem um único evento. Este comportamento pode mudar no futuro.

Pode encontrar o esquema JSON para o evento Event Grid e a carga de dados de cada editor da Azure na [loja De Schema do Evento.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)

## <a name="event-schema"></a>Esquema de eventos

O exemplo a seguir mostra as propriedades que são utilizadas por todos os editores de eventos:

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

Por exemplo, o esquema publicado para um evento de armazenamento Azure Blob é:

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

Todos os eventos têm os mesmos dados de alto nível:

| Propriedade | Tipo | Necessário | Descrição |
| -------- | ---- | -------- | ----------- |
| tópico | string | Não, mas se incluído, deve corresponder ao tópico de Classificação de Eventos Azure Resource Manager ID exatamente. Se não estiver incluído, a Grade de Eventos irá carimbar o evento. | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| subject | string | Yes | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Yes | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | Yes | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Yes | Identificador único para o evento. |
| dados | objeto | No | Dados do evento específicos para o fornecedor de recursos. |
| dataVersion | string | Não, mas será carimbado com um valor vazio. | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | Não é necessário, mas se incluído, deve corresponder exatamente ao Esquema de Grelha de Evento `metadataVersion` (atualmente, `1` apenas). Se não estiver incluído, a Grade de Eventos irá carimbar o evento. | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

Para saber mais sobre as propriedades no objeto de dados, consulte a fonte do evento:

* [Assinaturas Azure (operações de gestão)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Armazenamento de blobs](event-schema-blob-storage.md)
* [Hubs de Eventos](event-schema-event-hubs.md)
* [Hub IoT](event-schema-iot-hub.md)
* [Serviços de Multimédia](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupos de recursos (operações de gestão)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Para tópicos personalizados, o editor do evento determina o objeto de dados. Os dados de alto nível devem ter os mesmos campos que os eventos definidos pelos recursos padrão.

Ao publicar eventos para tópicos personalizados, crie temas para os seus eventos que facilitem aos subscritores saber se estão interessados no evento. Os assinantes utilizam o sujeito para eventos de filtragem e rotas. Considere fornecer o caminho para onde o evento aconteceu, para que os subscritores possam filtrar por segmentos desse caminho. O caminho permite que os assinantes filtram eventos de forma estreita ou ampla. Por exemplo, se fornecer um caminho de três segmentos como `/A/B/C` no assunto, os subscritores podem filtrar pelo primeiro segmento `/A` para obter um vasto conjunto de eventos. Esses assinantes obtêm eventos com temas como `/A/B/C` ou `/A/D/E` . Outros subscritores podem filtrar `/A/B` para obter um conjunto mais restrito de eventos.

Às vezes, o seu assunto precisa de mais detalhes sobre o que aconteceu. Por exemplo, o editor **de Contas de Armazenamento** fornece o assunto quando um ficheiro é adicionado a um `/blobServices/default/containers/<container-name>/blobs/<file>` recipiente. Um assinante poderia filtrar pelo caminho `/blobServices/default/containers/testcontainer` para obter todos os eventos para aquele contentor, mas não outros contentores na conta de armazenamento. Um assinante também pode filtrar ou encaminhar pelo sufixo `.txt` para apenas trabalhar com ficheiros de texto.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).

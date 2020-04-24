---
title: Esquema de evento sinuoso do evento Azure Event Grid
description: Descreve as propriedades e esquemas que estão presentes para todos os eventos.Os eventos consistem num conjunto de cinco propriedades de cordas necessárias e num objeto de dados necessário.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 7c45b8f634868024a84f9f3b75bb23031c09b40c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114008"
---
# <a name="azure-event-grid-event-schema"></a>Esquema de evento sinuoso do evento Azure Event Grid

Este artigo descreve as propriedades e esquemas que estão presentes para todos os eventos.Os eventos consistem num conjunto de cinco propriedades de cordas necessárias e num objeto de dados necessário. As propriedades são comuns a todos os eventos de qualquer editor. O objeto de dados tem propriedades específicas para cada editor. Para tópicos do sistema, estas propriedades são específicas para o fornecedor de recursos, como o Azure Storage ou o Azure Event Hubs.

Fontes do evento enviam eventos para a Azure Event Grid numa matriz, que pode ter vários objetos de eventos. Ao publicar eventos para um tópico de grelha de eventos, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz está limitado a 1 MB. Se um evento ou a matriz for maior do que os limites de tamanho, receberá a resposta **413 Payload Too Large**. As operações são carregadas em incrementos de 64 KB. Assim, eventos acima de 64 KB incorrerão em acusações de operações como se fossem múltiplos eventos. Por exemplo, um evento de 130 KB incorreria em operações como se fossem 3 eventos separados.

A Event Grid envia os eventos aos assinantes numa matriz que tem um único evento. Este comportamento pode mudar no futuro.

Você pode encontrar o esquema JSON para o evento Event Grid e a carga de dados de cada editor Azure na [loja Event Schema](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Esquema de eventos

O exemplo que se segue mostra as propriedades que são usadas por todos os editores de eventos:

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
| tópico | string | Não, mas se incluído, deve corresponder exatamente ao tópico da Grelha de Eventos Azure Resource Manager ID. Caso não seja incluído, a Grelha de Eventos irá carimbar no evento. | Caminho de recursos completos para a fonte do evento. Este campo não é reelegível. O Event Grid fornece este valor. |
| Assunto | string | Sim | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Sim | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | Sim | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Sim | Identificador único para o evento. |
| data | objeto | Não | Dados do evento específicos do fornecedor de recursos. |
| dataVersion | string | Não, mas será carimbado com um valor vazio. | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | Não é necessário, mas se incluído, deve coincidir `metadataVersion` exatamente com `1`o Event Grid Schema (atualmente, apenas). Caso não seja incluído, a Grelha de Eventos irá carimbar no evento. | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

Para saber mais sobre as propriedades do objeto de dados, consulte a fonte do evento:

* [Assinaturas Azure (operações de gestão)](event-schema-subscriptions.md)
* [Registo de Contentor](event-schema-container-registry.md)
* [Armazenamento de bolhas](event-schema-blob-storage.md)
* [Hubs de Eventos](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Serviços de Multimédia](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupos de recursos (operações de gestão)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Para tópicos personalizados, a editora de eventos determina o objeto de dados. Os dados de alto nível devem ter os mesmos campos que os eventos definidos por recursos padrão.

Ao publicar eventos para tópicos personalizados, crie temas para os seus eventos que facilitem aos assinantes saber se estão interessados no evento. Os assinantes usam o sujeito para filtrar e route events. Considere providenciar o caminho para onde o evento aconteceu, para que os assinantes possam filtrar por segmentos desse caminho. O caminho permite aos assinantes filtrar eventos de forma estreita ou ampla. Por exemplo, se fornecer um `/A/B/C` caminho de três segmentos como no `/A` assunto, os assinantes podem filtrar pelo primeiro segmento para obter um vasto conjunto de eventos. Esses assinantes recebem eventos `/A/B/C` `/A/D/E`com temas como ou . Outros subscritores podem `/A/B` filtrar para obter um conjunto mais restrito de eventos.

Às vezes, o seu assunto precisa de mais detalhes sobre o que aconteceu. Por exemplo, o editor de Contas `/blobServices/default/containers/<container-name>/blobs/<file>` de **Armazenamento** fornece o assunto quando um ficheiro é adicionado a um recipiente. Um assinante poderia filtrar pelo caminho `/blobServices/default/containers/testcontainer` para obter todos os eventos para esse contentor, mas não outros contentores na conta de armazenamento. Um assinante também pode filtrar ou `.txt` fazer a rota pelo sufixo para trabalhar apenas com ficheiros de texto.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).

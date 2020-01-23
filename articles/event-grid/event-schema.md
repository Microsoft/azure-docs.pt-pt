---
title: Esquema de evento da grade de eventos do Azure
description: Descreve as propriedades e o esquema que estão presentes para todos os eventos. Os eventos consistem em um conjunto de cinco Propriedades de cadeia de caracteres necessárias e um objeto de dados necessário.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 1fceda6fcbb6e8db1fa8afbc5181315bd0c98940
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512985"
---
# <a name="azure-event-grid-event-schema"></a>Esquema de evento da grade de eventos do Azure

Este artigo descreve as propriedades e o esquema que estão presentes para todos os eventos. Os eventos consistem em um conjunto de cinco Propriedades de cadeia de caracteres necessárias e um objeto de dados necessário. As propriedades são comuns a todos os eventos de qualquer editor. O objeto de dados tem propriedades que são específicas para cada publicador. Para tópicos do sistema, essas propriedades são específicas para o provedor de recursos, como o armazenamento do Azure ou os hubs de eventos do Azure.

Origens de eventos enviam eventos para a grade de eventos do Azure em uma matriz, que pode ter vários objetos de evento. Ao postar eventos em um tópico da grade de eventos, a matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz é limitado a 64 KB (disponibilidade geral) ou 1 MB (versão prévia). Se um evento ou a matriz for maior que os limites de tamanho, você receberá a carga de resposta **413 muito grande**.

> [!NOTE]
> Um evento de tamanho de até 64 KB é coberto pela disponibilidade geral (GA) Contrato de Nível de Serviço (SLA). O suporte para um evento de tamanho de até 1 MB está atualmente em visualização. Eventos acima de 64 KB são cobrados em incrementos de 64 KB. 

A grade de eventos envia os eventos aos assinantes em uma matriz que tem um único evento. Esse comportamento pode ser alterado no futuro.

Você pode encontrar o esquema JSON para o evento da grade de eventos e a carga de dados de cada editor do Azure no [repositório de esquemas de eventos](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Esquema de eventos

O exemplo a seguir mostra as propriedades que são usadas por todos os editores de eventos:

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

Por exemplo, o esquema publicado para um evento de armazenamento de BLOBs do Azure é:

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

Todos os eventos têm os mesmos dados de nível superior a seguir:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho completo do recurso para a origem do evento. Este campo não é gravável. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de evento específicos para o provedor de recursos. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

Para saber mais sobre as propriedades no objeto de dados, consulte a origem do evento:

* [Assinaturas do Azure (operações de gerenciamento)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Armazenamento de blobs](event-schema-blob-storage.md)
* [Hubs de Eventos](event-schema-event-hubs.md)
* [Hub IoT](event-schema-iot-hub.md)
* [Serviços de Multimédia](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Grupos de recursos (operações de gerenciamento)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Sinalizador do Azure](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Para tópicos personalizados, o editor de eventos determina o objeto de dados. Os dados de nível superior devem ter os mesmos campos que os eventos definidos pelo recurso padrão.

Ao publicar eventos em tópicos personalizados, crie assuntos para seus eventos, o que torna mais fácil para os assinantes saber se eles estão interessados no evento. Os assinantes usam o assunto para filtrar e rotear eventos. Considere fornecer o caminho para onde o evento ocorreu, para que os assinantes possam filtrar por segmentos desse caminho. O caminho permite que os assinantes filtrem eventos de forma estreita ou ampla. Por exemplo, se você fornecer um caminho de três segmentos como `/A/B/C` no assunto, os assinantes poderão filtrar pelo primeiro segmento `/A` para obter um amplo conjunto de eventos. Esses assinantes recebem eventos com assuntos como `/A/B/C` ou `/A/D/E`. Outros assinantes podem filtrar por `/A/B` para obter um conjunto mais estreito de eventos.

Às vezes, seu assunto precisa de mais detalhes sobre o que aconteceu. Por exemplo, o editor de **contas de armazenamento** fornece a entidade `/blobServices/default/containers/<container-name>/blobs/<file>` quando um arquivo é adicionado a um contêiner. Um assinante pode filtrar pelo caminho `/blobServices/default/containers/testcontainer` para obter todos os eventos para esse contêiner, mas não para outros contêineres na conta de armazenamento. Um assinante também pode filtrar ou rotear pelo sufixo `.txt` para trabalhar apenas com arquivos de texto.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).

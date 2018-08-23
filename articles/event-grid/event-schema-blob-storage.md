---
title: Esquema do evento de armazenamento de Blobs do Azure Event Grid
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de Blobs com o Azure Event Grid
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: 11524f8868a0102e30b06f3385a26b1bd06aae6e
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/17/2018
ms.locfileid: "42060267"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Esquema de eventos do Azure Event Grid para armazenamento de BLOBs

Este artigo fornece as propriedades e o esquema para eventos de armazenamento de Blobs. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](event-schema.md).

Para obter uma lista de scripts de exemplo e tutoriais, consulte [origem de eventos de armazenamento](event-sources.md#storage).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

Armazenamento de BLOBs emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Desencadeado quando um blob é criado. |
| Microsoft.Storage.BlobDeleted | Desencadeado quando um blob é eliminado. |

## <a name="example-event"></a>Evento de exemplo

O exemplo seguinte mostra o esquema de um blob criado eventos: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

O esquema para um evento de blob eliminado é semelhante: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | cadeia | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Event Grid fornece este valor. |
| Assunto | cadeia | Caminho definidos pelo publicador para o assunto de evento. |
| eventType | cadeia | Um dos tipos de eventos registrados para esta origem de evento. |
| eventTime | cadeia | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| ID | cadeia | Identificador exclusivo para o evento. |
| dados | objeto | Dados de eventos de armazenamento de Blobs. |
| dataVersion | cadeia | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | cadeia | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | cadeia | A operação que acionou o evento. |
| clientRequestId | cadeia | Um valor gerado pelo cliente, opaco com um limite de carateres de 1 KB. Quando tiver ativado o registo de análise de armazenamento, ele será gravado em registos de análise. |
| requestId | cadeia | O identificador exclusivo para o pedido. Utilize-o para o pedido de resolução de problemas. |
| eTag | cadeia | O valor que pode utilizar para efetuar operações de forma condicional. |
| contentType | cadeia | O tipo de conteúdo especificado para o blob. |
| contentLength | inteiro | O tamanho do blob em bytes. |
| blobType | cadeia | O tipo de blob. Valores válidos são "BlockBlob" ou "PageBlob". |
| url | cadeia | O caminho para o blob. |
| Sequenciador | cadeia | Um valor controlada pelo utilizador que pode utilizar para monitorizar os pedidos. |
| storageDiagnostics | objeto | Informações sobre os diagnósticos de armazenamento. |
 
## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
* Para obter uma introdução para trabalhar com eventos de armazenamento de BLOBs, veja [eventos de armazenamento de BLOBs de rotas - CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 

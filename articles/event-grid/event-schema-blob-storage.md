---
title: Esquema do evento de armazenamento de Blobs do Azure Event Grid
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de Blobs com o Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445772"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Esquema de eventos do Azure Event Grid para armazenamento de BLOBs

Este artigo fornece as propriedades e o esquema para eventos de armazenamento de Blobs. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](event-schema.md).

Para obter uma lista de scripts de exemplo e tutoriais, consulte [origem de eventos de armazenamento](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Lista de eventos para as APIs REST do Blob

Esses eventos são acionados quando um cliente cria, substitui ou elimina um blob ao chamar APIs de REST do Blob.

 |Nome do evento |Descrição|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Acionado quando um blob é criado ou substituído. <br>Especificamente, este evento é acionado quando os clientes utilizam o `PutBlob`, `PutBlockList`, ou `CopyBlob` operações que estão disponíveis na API de REST do Blob.   |
 |**Microsoft.Storage.BlobDeleted** |Acionado quando um blob é eliminado. <br>Especificamente, este evento é acionado quando os clientes chamam o `DeleteBlob` operação que está disponível na API de REST do Blob. |

> [!NOTE]
> Se pretender certificar-se de que o **Microsoft.Storage.BlobCreated** evento é acionado apenas quando um Blob de blocos está totalmente comprometido, filtrar o evento para o `CopyBlob`, `PutBlob`, e `PutBlockList` chamadas à REST API. Estes acionador de chamadas de API a **Microsoft.Storage.BlobCreated** evento apenas depois de dados estão totalmente comprometidos com um Blob de blocos. Para saber como criar um filtro, veja [filtrar eventos do Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista dos eventos para o Azure Data Lake Storage Gen 2 REST APIs

Esses eventos são acionados se ativar um espaço de nomes hierárquico na conta de armazenamento e os clientes chamam APIs de REST do Gen2 de armazenamento do Azure Data Lake.

> [!NOTE]
> Estes eventos estão em pré-visualização pública e estão disponíveis apenas a **E.U.A. oeste 2** e **e.u.a. Centro-Oeste** regiões.

 |Nome do evento|Descrição|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Acionado quando um blob é criado ou substituído. <br>Especificamente, este evento é acionado quando os clientes utilizam o `CreateFile` e `FlushWithClose` operações que estão disponíveis na API de REST de geração 2 de armazenamento do Azure Data Lake. |
 |**Microsoft.Storage.BlobDeleted** |Acionado quando um blob é eliminado. <br>Especificamente, este evento também é acionado quando os clientes chamam o `DeleteFile` operação que está disponível na API de REST de geração 2 de armazenamento do Azure Data Lake. |
 |**Microsoft.Storage.BlobRenamed**|Acionado quando um blob for renomeado. <br>Especificamente, este evento é acionado quando os clientes utilizam o `RenameFile` operação que está disponível na API de REST de geração 2 de armazenamento do Azure Data Lake.|
 |**Microsoft.Storage.DirectoryCreated**|Acionado quando é criado um diretório. <br>Especificamente, este evento é acionado quando os clientes utilizam o `CreateDirectory` operação que está disponível na API de REST de geração 2 de armazenamento do Azure Data Lake.|
 |**Microsoft.Storage.DirectoryRenamed**|Acionado quando um diretório foi mudado. <br>Especificamente, este evento é acionado quando os clientes utilizam o `RenameDirectory` operação que está disponível na API de REST de geração 2 de armazenamento do Azure Data Lake.|
 |**Microsoft.Storage.DirectoryDeleted**|Acionado quando um diretório é eliminado. <br>Especificamente, este evento é acionado quando os clientes utilizam o `DeleteDirectory` operação que está disponível na API de REST de geração 2 de armazenamento do Azure Data Lake.|

> [!NOTE]
> Se pretender certificar-se de que o **Microsoft.Storage.BlobCreated** evento é acionado apenas quando um Blob de blocos está totalmente comprometido, filtrar o evento para o `FlushWithClose` chamada à REST API. Acionadores de chamar esta API a **Microsoft.Storage.BlobCreated** evento apenas depois de dados estão totalmente comprometidos com um Blob de blocos. Para saber como criar um filtro, veja [filtrar eventos do Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta a eventos

Quando um evento é disparado, o serviço de Event Grid envia dados sobre esse evento para o ponto final de inscrição.

Esta secção contém um exemplo de como ficaria esses dados para cada evento de armazenamento de Blobs.

### <a name="microsoftstorageblobcreated-event"></a>Evento de Microsoft.Storage.BlobCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
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
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Evento de Microsoft.Storage.BlobCreated (Gen2 de armazenamento do Data Lake)

Se a conta de armazenamento de BLOBs tem um espaço de nomes hierárquico, os dados é semelhantes ao exemplo anterior com a exceção a estas alterações:

* O `dataVersion` chave está definida como um valor de `2`.

* O `data.api` chave é definida como a cadeia de caracteres `CreateFile` ou `FlushWithClose`.

* O `contentOffset` chave está incluída no conjunto de dados.

> [!NOTE]
> Se os aplicativos usam o `PutBlockList` operação para carregar um blob novo para a conta, os dados não conter essas alterações.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Evento de Microsoft.Storage.BlobDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Evento de Microsoft.Storage.BlobDeleted (Gen2 de armazenamento do Data Lake)

Se a conta de armazenamento de BLOBs tem um espaço de nomes hierárquico, os dados é semelhantes ao exemplo anterior com a exceção a estas alterações:

* O `dataVersion` chave está definida como um valor de `2`.

* O `data.api` chave é definida como a cadeia de caracteres `DeleteFile`.

* O `url` chave contém o caminho `dfs.core.windows.net`.

> [!NOTE]
> Se os aplicativos usam o `DeleteBlob` operação para eliminar um blob da conta, os dados não conter essas alterações.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Evento de Microsoft.Storage.BlobRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Evento de Microsoft.Storage.DirectoryCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Evento de Microsoft.Storage.DirectoryRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Evento de Microsoft.Storage.DirectoryDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
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
| data | objeto | Dados de eventos de armazenamento de Blobs. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que acionou o evento. |
| clientRequestId | string | um id de pedido fornecido pelo cliente para o armazenamento de operação de API. Este id pode ser utilizado para correlacionar para registos de diagnóstico de armazenamento do Azure com o campo de "client-request-id" nos registos e pode ser fornecida nos pedidos de cliente com o cabeçalho "x-ms-client-request-id". Ver [formato de registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Id do pedido geradas pelo serviço para a operação de API de armazenamento. Pode ser utilizado para correlacionar ao armazenamento do Azure registos com o campo de "cabeçalho de id de pedido" nos registos de diagnóstico e é devolvido de iniciar a chamada de API no cabeçalho "x-ms-pedido-id". Ver [formato de registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que pode utilizar para efetuar operações de forma condicional. |
| contentType | string | O tipo de conteúdo especificado para o blob. |
| contentLength | inteiro | O tamanho do blob em bytes. |
| blobType | string | O tipo de blob. Valores válidos são "BlockBlob" ou "PageBlob". |
| contentOffset | número | O deslocamento em bytes de uma operação de escrita executadas no ponto em que o aplicativo de acionar o evento completou a gravação para o ficheiro. <br>É apresentada apenas para eventos acionados em contas de armazenamento de BLOBs que têm um espaço de nomes hierárquico.|
| destinationUrl |string | O url do ficheiro que existirá após a conclusão da operação. Por exemplo, se um ficheiro for renomeado, o `destinationUrl` propriedade contém a url do nome do ficheiro novo. <br>É apresentada apenas para eventos acionados em contas de armazenamento de BLOBs que têm um espaço de nomes hierárquico.|
| sourceUrl |string | O url do ficheiro que existe antes da operação. Por exemplo, se um ficheiro for renomeado, o `sourceUrl` contém o url do nome do ficheiro original antes da operação de mudança de nome. <br>É apresentada apenas para eventos acionados em contas de armazenamento de BLOBs que têm um espaço de nomes hierárquico. |
| url | string | O caminho para o blob. <br>Se o cliente utiliza uma API de REST de BLOBs, em seguida, o url tem esta estrutura:  *\<nome da conta de armazenamento\>.blob.core.windows.net/\<nome do contentor\>/\<nome de ficheiro \>* . <br>Se o cliente utiliza uma API de REST de armazenamento do Data Lake, em seguida, o url tem esta estrutura:  *\<nome da conta de armazenamento\>.dfs.core.windows.net/\<nome de sistema de ficheiros\> / \<nome de ficheiro\>* .
|
| recursive| string| `True` para executar a operação em todos os diretórios filho; caso contrário `False`. <br>É apresentada apenas para eventos acionados em contas de armazenamento de BLOBs que têm um espaço de nomes hierárquico. |
| sequencer | string | Um valor de cadeia opaca que representa a sequência lógica de eventos para qualquer nome de determinado blob.  Os utilizadores podem utilizar a comparação de cadeias de caracteres padrão para compreender a sequência relativa de dois eventos sobre o mesmo nome de blob. |
| storageDiagnostics | object | Dados de diagnóstico, ocasionalmente, incluídos pelo serviço de armazenamento do Azure. Quando estiver presente, deve ser ignorado pelos consumidores de eventos. |

|Propriedade|Tipo|Descrição|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
* Para obter uma introdução para trabalhar com eventos de armazenamento de BLOBs, veja [eventos de armazenamento de BLOBs de rotas - CLI do Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 

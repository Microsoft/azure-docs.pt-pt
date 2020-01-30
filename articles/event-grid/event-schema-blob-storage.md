---
title: Esquema de armazenamento de blob da Azure Event Grid
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de blob com azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 4a71f50a130bd9b22965d39fa942b47c70857a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844484"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Esquema de evento sinuoso do evento Azure Event Grid para armazenamento blob

Este artigo fornece as propriedades e esquemas para eventos de armazenamento de bolhas. Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md).

Para obter uma lista de scripts e tutoriais de amostra, consulte a fonte do [evento de armazenamento.](event-sources.md#storage)

>[!NOTE]
> Apenas contas de armazenamento do tipo **StorageV2 (finalidade geral v2)** e integração de eventos de suporte **blobStorage.** **O armazenamento (propósito genral v1)** *não* suporta a integração com a Grelha de Eventos.

## <a name="list-of-events-for-blob-rest-apis"></a>Lista de eventos para APIs de REPOUSO Blob

Estes eventos são desencadeados quando um cliente cria, substitui ou elimina uma bolha chamando a Blob REST APIs.

 |Nome do evento |Descrição|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Desencadeado quando uma bolha é criada ou substituída. <br>Especificamente, este evento é desencadeado quando os clientes usam as operações `PutBlob`, `PutBlockList`ou `CopyBlob` que estão disponíveis na API BLOB REST.   |
 |**Microsoft.Storage.BlobDeleted** |Desencadeado quando uma bolha é apagada. <br>Especificamente, este evento é desencadeado quando os clientes chamam a operação `DeleteBlob` que está disponível na API Blob REST. |

> [!NOTE]
> Se quiser garantir que o evento **Microsoft.Storage.BlobCreated** só é acionado quando um Block Blob estiver completamente comprometido, filtre o evento para o `CopyBlob`, `PutBlob`e `PutBlockList` chamadas API REST. Estas chamadas API desencadeiam o evento **Microsoft.Storage.BlobCreated** apenas após os dados estartotalmente comprometidos com um Block Blob. Para aprender a criar um filtro, consulte [os eventos de filtragem para a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista dos eventos para Azure Data Lake Storage Gen 2 REST APIs

Estes eventos são desencadeados se permitir um espaço de nome hierárquico na conta de armazenamento, e os clientes chamam Azure Data Lake Storage Gen2 REST APIs.

|Nome do evento|Descrição|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Desencadeado quando uma bolha é criada ou substituída. <br>Especificamente, este evento é desencadeado quando os clientes usam as operações `CreateFile` e `FlushWithClose` que estão disponíveis na API REST REST Do Lago De dados Azure. |
|**Microsoft.Storage.BlobDeleted** |Desencadeado quando uma bolha é apagada. <br>Especificamente, este evento também é desencadeado quando os clientes ligam para a operação `DeleteFile` que está disponível na API REST REST Do Lago De dados Azure. |
|**Microsoft.Storage.BlobRenamed**|Desencadeado quando uma bolha é renomeada. <br>Especificamente, este evento é desencadeado quando os clientes usam a operação `RenameFile` que está disponível na API REST REST Do Lago De dados Azure.|
|**Microsoft.Storage.DirectoryCreated**|Desencadeado quando um diretório é criado. <br>Especificamente, este evento é desencadeado quando os clientes usam a operação `CreateDirectory` que está disponível na API REST REST Do Lago De dados Azure.|
|**Microsoft.Storage.DirectoryRenamed**|Desencadeado quando um diretório é renomeado. <br>Especificamente, este evento é desencadeado quando os clientes usam a operação `RenameDirectory` que está disponível na API REST REST Do Lago De dados Azure.|
|**Microsoft.Storage.DirectoryDeleted**|Desencadeado quando um diretório é apagado. <br>Especificamente, este evento é desencadeado quando os clientes usam a operação `DeleteDirectory` que está disponível na API REST REST Do Lago De dados Azure.|

> [!NOTE]
> Se quiser garantir que o evento **Microsoft.Storage.BlobCreated** só é acionado quando um Block Blob estiver completamente comprometido, filtre o evento para a chamada `FlushWithClose` REST API. Esta chamada API despoleta o evento **Microsoft.Storage.BlobCreated** apenas depois de os dados estiverem totalmente comprometidos com um Block Blob. Para aprender a criar um filtro, consulte [os eventos de filtragem para a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

Quando um evento é desencadeado, o serviço De Event Grid envia dados sobre esse evento para subscrever endpoint.

Esta secção contém um exemplo do que esses dados seriam para cada evento de armazenamento de bolhas.

### <a name="microsoftstorageblobcreated-event"></a>Evento Microsoft.Storage.BlobCreated

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated event (Data Lake Storage Gen2)

Se a conta de armazenamento blob tiver um espaço de nome hierárquico, os dados são semelhantes ao exemplo anterior, com exceção destas alterações:

* A chave `dataVersion` está definida para um valor de `2`.

* A chave `data.api` está definida para a `CreateFile` ou `FlushWithClose`de cadeias.

* A chave `contentOffset` está incluída no conjunto de dados.

> [!NOTE]
> Se as aplicações utilizarem a operação `PutBlockList` para fazer o upload de uma nova bolha para a conta, os dados não conterão estas alterações.

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
    "eTag": "\"0x8D4BCC2E4835CD0\"",
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

### <a name="microsoftstorageblobdeleted-event"></a>Evento Microsoft.Storage.BlobDeleted

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted event (Data Lake Storage Gen2)

Se a conta de armazenamento blob tiver um espaço de nome hierárquico, os dados são semelhantes ao exemplo anterior, com exceção destas alterações:

* A chave `dataVersion` está definida para um valor de `2`.

* A chave `data.api` está definida para a `DeleteFile`de cordas .

* A chave `url` contém o caminho `dfs.core.windows.net`.

> [!NOTE]
> Se as aplicações utilizarem a operação `DeleteBlob` para eliminar uma bolha da conta, os dados não conterão estas alterações.

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

### <a name="microsoftstorageblobrenamed-event"></a>Evento Microsoft.Storage.BlobRenomeado

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

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated evento

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed event

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted event

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
| tópico | string | Caminho completo do recurso para a origem do evento. Este campo não é gravável. A Grelha de Eventos fornece este valor. |
| subject | string | Caminho definido pela editora para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta fonte do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de evento de armazenamento de BLOBs. |
| dataVersion | string | A versão esquema do objeto de dados. A editora define a versão do esquema. |
| metadataVersion | string | A versão de esquema dos metadados do evento. A Grelha de Eventos define o esquema das propriedades de alto nível. A Grelha de Eventos fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que disparou o evento. |
| clientRequestId | string | uma ID de solicitação fornecida pelo cliente para a operação da API de armazenamento. Essa ID pode ser usada para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "Client-Request-ID" nos logs e pode ser fornecida em solicitações de cliente usando o cabeçalho "x-MS-Client-Request-ID". Consulte [formato de log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID da solicitação gerada pelo serviço para a operação da API de armazenamento. Pode ser usado para correlacionar os logs de diagnóstico do armazenamento do Azure usando o campo "solicitação-ID-cabeçalho" nos logs e é retornado de iniciar chamada à API no cabeçalho ' x-MS-Request-ID '. Consulte [formato de log](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que você pode usar para executar operações condicionalmente. |
| contentType | string | O tipo de conteúdo especificado para o blob. |
| contentLength | número inteiro | O tamanho do blob em bytes. |
| blobType | string | O tipo de BLOB. Os valores válidos são "BlockBlob" ou "PageBlob". |
| contentOffset | número | A compensação em bytes de uma operação de escrita realizada no ponto em que a aplicação de desencadeamento de eventos completou a escrita para o ficheiro. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço de nome hierárquico.|
| destinationUrl |string | O url do ficheiro que existirá após a operação estar concluída. Por exemplo, se um ficheiro for renomeado, a propriedade `destinationUrl` contém o url do novo nome do ficheiro. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço de nome hierárquico.|
| sourceUrl |string | O url do ficheiro que existe antes da operação. Por exemplo, se um ficheiro for renomeado, o `sourceUrl` contém o url do nome de ficheiro original antes da operação de renome. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço de nome hierárquico. |
| url | string | O caminho para o blob. <br>Se o cliente usar uma API REST de BLOB, a URL terá essa estrutura: *\<Storage-Account-name\>. blob.core.windows.net/\<nome-do-contêiner\>/\<nome-do-arquivo\>* . <br>Se o cliente usar uma API REST Data Lake Storage, a URL terá essa estrutura: *\<nome-da-conta de armazenamento\>. dfs.core.windows.net/\<arquivo-System-name\>/\<nome-* do-arquivo\>. |
| recursive | string | `True` realizar a operação em todas as direções infantis; caso contrário, `False`. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço de nome hierárquico. |
| sequencer | string | Um valor de corda opaco que representa a sequência lógica de eventos para qualquer nome blob em particular.  Os utilizadores podem usar a comparação padrão de cordas para entender a sequência relativa de dois eventos no mesmo nome blob. |
| armazenamentoDiagnósticos | objeto | Dados de diagnóstico ocasionalmente incluídos pelo serviço de Armazenamento Azure. Quando presente, deve ser ignorado pelos consumidores de eventos. |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução à grade de eventos do Azure, consulte [o que é a grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da grade de eventos do Azure, consulte [esquema de assinatura da grade de eventos](subscription-creation-schema.md).
* Para uma introdução ao trabalho com eventos de armazenamento de blob, consulte eventos de [armazenamento Route Blob - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 

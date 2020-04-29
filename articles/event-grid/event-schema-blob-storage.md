---
title: Armazenamento de Blob Azure como fonte da Grelha de Eventos
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de blob com azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 8d22f8a2722dc55a13ce8e3752ca69d6e7251070
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115130"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Armazenamento azure Blob como fonte da Grelha de Eventos

Este artigo fornece as propriedades e esquemas para eventos de armazenamento de bolhas.Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md). Também lhe dá uma lista de arranques rápidos e tutoriais para usar o Armazenamento Azure Blob como fonte de evento.


>[!NOTE]
> Apenas contas de armazenamento de tipo **StorageV2 (finalidade geral v2)**, **BlockBlobStorage**e integração de eventos de suporte **blobStorage.** **O armazenamento (finalidade geral v1)** *não* suporta a integração com a Grelha de Eventos.

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="list-of-events-for-blob-rest-apis"></a>Lista de eventos para APIs de REPOUSO Blob

Estes eventos são desencadeados quando um cliente cria, substitui ou elimina uma bolha chamando a Blob REST APIs.

 |Nome do evento |Descrição|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Desencadeado quando uma bolha é criada ou substituída. <br>Especificamente, este evento é desencadeado `PutBlob`quando `PutBlockList`os `CopyBlob` clientes usam a , ou operações que estão disponíveis na API BLOB REST.   |
 |**Microsoft.Storage.BlobDeleted** |Desencadeado quando uma bolha é apagada. <br>Especificamente, este evento é desencadeado `DeleteBlob` quando os clientes ligam para a operação que está disponível na API Blob REST. |

> [!NOTE]
> Se quiser garantir que o evento **Microsoft.Storage.BlobCreated** só é acionado quando um Block `CopyBlob`Blob estiver completamente comprometido, filtre o evento para as `PutBlob`chamadas , e `PutBlockList` REST API. Estas chamadas API desencadeiam o evento **Microsoft.Storage.BlobCreated** apenas após os dados estartotalmente comprometidos com um Block Blob. Para aprender a criar um filtro, consulte [os eventos de filtragem para a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista dos eventos para Azure Data Lake Storage Gen 2 REST APIs

Estes eventos são desencadeados se permitir um espaço de nome hierárquico na conta de armazenamento, e os clientes chamam Azure Data Lake Storage Gen2 REST APIs. Para mais informações sobre o Azure Data Lake Storage Gen2, consulte introdução ao Armazenamento de [Lagos De dados Azure Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Nome do evento|Descrição|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Desencadeado quando uma bolha é criada ou substituída. <br>Especificamente, este evento é desencadeado `CreateFile` quando `FlushWithClose` os clientes usam as operações e operações que estão disponíveis na API REST REST Do Lago De dados Azure. |
|**Microsoft.Storage.BlobDeleted** |Desencadeado quando uma bolha é apagada. <br>Especificamente, este evento também é desencadeado `DeleteFile` quando os clientes ligam para a operação que está disponível na API REST REST Do Lago De dados Azure. |
|**Microsoft.Storage.BlobRenamed**|Desencadeado quando uma bolha é renomeada. <br>Especificamente, este evento é desencadeado `RenameFile` quando os clientes utilizam a operação que está disponível na API REST De Armazenamento de Lago de Dados Azure.|
|**Microsoft.Storage.DirectoryCreated**|Desencadeado quando um diretório é criado. <br>Especificamente, este evento é desencadeado `CreateDirectory` quando os clientes utilizam a operação que está disponível na API REST De Armazenamento de Lago de Dados Azure.|
|**Microsoft.Storage.DirectoryRenomeado**|Desencadeado quando um diretório é renomeado. <br>Especificamente, este evento é desencadeado `RenameDirectory` quando os clientes utilizam a operação que está disponível na API REST De Armazenamento de Lago de Dados Azure.|
|**Microsoft.Storage.DirectoryDeleted**|Desencadeado quando um diretório é apagado. <br>Especificamente, este evento é desencadeado `DeleteDirectory` quando os clientes utilizam a operação que está disponível na API REST De Armazenamento de Lago de Dados Azure.|

> [!NOTE]
> Se quiser garantir que o evento **Microsoft.Storage.BlobCreated** só é acionado quando um Block `FlushWithClose` Blob estiver completamente comprometido, filtre o evento para a chamada REST API. Esta chamada API despoleta o evento **Microsoft.Storage.BlobCreated** apenas depois de os dados estiverem totalmente comprometidos com um Block Blob. Para aprender a criar um filtro, consulte [os eventos de filtragem para a Grelha de Eventos](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

### <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta de evento

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

* A `dataVersion` chave está definida `2`para um valor de .

* A `data.api` chave está definida `CreateFile` `FlushWithClose`na corda ou .

* A `contentOffset` chave está incluída no conjunto de dados.

> [!NOTE]
> Se as aplicações utilizarem a `PutBlockList` operação para fazer o upload de uma nova bolha para a conta, os dados não conterão estas alterações.

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

* A `dataVersion` chave está definida `2`para um valor de .

* A `data.api` chave está definida `DeleteFile`na corda.

* A `url` chave contém `dfs.core.windows.net`o caminho.

> [!NOTE]
> Se as aplicações utilizarem a `DeleteBlob` operação para eliminar uma bolha da conta, os dados não conterão estas alterações.

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

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento de armazenamento blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que desencadeou o evento. |
| clientRequestId | string | um id de pedido fornecido pelo cliente para a operação API de armazenamento. Este id pode ser usado para correlacionar com os registos de diagnóstico do Armazenamento Azure utilizando o campo "cliente-request-id" nos registos, e pode ser fornecido em pedidos de cliente utilizando o cabeçalho "x-ms-cliente-request-id". Ver [Formato de Registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Id de pedido gerado pelo serviço para a operação API de armazenamento. Pode ser usado para correlacionar com os registos de diagnóstico do Armazenamento Azure utilizando o campo "request-id-header" nos registos e é devolvido a partir do início da chamada da API no cabeçalho 'x-ms-request-id'. Ver [Formato de Registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que pode utilizar para realizar operações condicionalmente. |
| conteúdoType | string | O tipo de conteúdo especificado para a bolha. |
| conteúdoComprimento | número inteiro | O tamanho da bolha em bytes. |
| blobType | string | O tipo de bolha. Os valores válidos são "BlockBlob" ou "PageBlob". |
| conteúdoOffset | número | A compensação em bytes de uma operação de escrita realizada no ponto em que a aplicação de desencadeamento de eventos completou a escrita para o ficheiro. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço de nome hierárquico.|
| destinoUrl |string | O url do ficheiro que existirá após a operação estar concluída. Por exemplo, se um ficheiro `destinationUrl` for renomeado, a propriedade contém o url do novo nome do ficheiro. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço de nome hierárquico.|
| fonteUrl |string | O url do ficheiro que existe antes da operação. Por exemplo, se um ficheiro `sourceUrl` for renomeado, o url do nome de ficheiro original antes da operação de renome. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço de nome hierárquico. |
| url | string | O caminho para a bolha. <br>Se o cliente utilizar uma API Blob REST, então o url tem esta estrutura: * \<nome de\>arma de armazenamento .blob.core.windows.net/\<nome\>/\<\>de ficheiro de nome de contentor*. <br>Se o cliente utilizar uma API REST De armazenamento de data Lake, então o url tem esta estrutura: * \<\>nome de arma de armazenamento .dfs.core.windows.net/\<nome\>/\<\>de ficheiro do ficheiro do ficheiro*. |
| recursivo | string | `True`executar a operação em todas as direções infantis; caso `False`contrário. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço de nome hierárquico. |
| sequenciador | string | Um valor de corda opaco que representa a sequência lógica de eventos para qualquer nome blob em particular.  Os utilizadores podem usar a comparação padrão de cordas para entender a sequência relativa de dois eventos no mesmo nome blob. |
| armazenamentoDiagnósticos | objeto | Dados de diagnóstico ocasionalmente incluídos pelo serviço de Armazenamento Azure. Quando presente, deve ser ignorado pelos consumidores de eventos. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título  |Descrição  |
|---------|---------|
| [Quickstart: encaminhe eventos de armazenamento blob para um ponto final web personalizado com Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de armazenamento blob para um WebHook. |
| [Quickstart: encaminhe eventos de armazenamento blob para um ponto final personalizado com powerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure PowerShell para enviar eventos de armazenamento de blob para um WebHook. |
| [Quickstart: criar e encaminhar eventos de armazenamento Blob com o portal Azure](blob-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos de armazenamento blob para um WebHook. |
| [Azure CLI: subscreva eventos para uma conta de armazenamento Blob](./scripts/event-grid-cli-blob.md) | Prove script que subscreve evento para uma conta de armazenamento Blob. Envia o evento para um WebHook. |
| [PowerShell: subscreva eventos para uma conta de armazenamento Blob](./scripts/event-grid-powershell-blob.md) | Prove script que subscreve evento para uma conta de armazenamento Blob. Envia o evento para um WebHook. |
| [Modelo de Gestor de Recursos: Criar armazenamento e subscrição blob](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Implementa uma conta de armazenamento de Blobs do Azure e subscreve eventos para essa conta de armazenamento. Envia eventos para um WebHook. |
| [Visão geral: reagir a eventos de armazenamento blob](../storage/blobs/storage-blob-event-overview.md) | Visão geral da integração do armazenamento blob com a Grelha de Eventos. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
* Para uma introdução ao trabalho com eventos de armazenamento de blob, consulte eventos de [armazenamento Route Blob - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 

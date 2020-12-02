---
title: Armazenamento Azure Blob como fonte de grade de eventos
description: Descreve as propriedades que são fornecidas para eventos de armazenamento de blob com Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1a81b30fcb775f5e8bc99bda70307f7f1aed9796
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452538"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Armazenamento Azure Blob como fonte de grade de eventos

Este artigo fornece as propriedades e esquema para eventos de armazenamento de bolhas. Para uma introdução aos esquemas de eventos, consulte [o esquema do evento Azure Event Grid](event-schema.md). Também lhe dá uma lista de partidas rápidas e tutoriais para usar o Azure Blob Storage como fonte de evento.


>[!NOTE]
> Apenas contas de armazenamento do tipo **StorageV2 (finalidade geral v2),** **BlockBlobStorage** e integração de eventos de suporte **BlobStorage.** **O armazenamento (propósito geral v1)** *não* suporta a integração com a Grade de Eventos.

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="list-of-events-for-blob-rest-apis"></a>Lista de eventos para Blob REST APIs

Estes eventos são desencadeados quando um cliente cria, substitui ou elimina uma bolha chamando APIs blob REST.

> [!NOTE]
> Os `$logs` `$blobchangefeed` contentores e contentores não estão integrados com a Grade de Eventos, pelo que a atividade nestes contentores não gera eventos. Além disso, a utilização do ponto final dfs *`(abfss://URI) `* para contas não hierárquicas ativadas não gerará eventos, mas o ponto final blob *`(wasb:// URI)`* gerará eventos.

 |Nome do evento |Descrição|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Acionado quando uma bolha é criada ou substituída. <br>Especificamente, este evento é desencadeado quando os clientes usam o `PutBlob` `PutBlockList` , ou `CopyBlob` operações que estão disponíveis na API Blob REST.   |
 |**Microsoft.Storage.BlobDeleted** |Acionado quando uma bolha é apagada. <br>Especificamente, este evento é desencadeado quando os clientes ligam para a `DeleteBlob` operação que está disponível na API Blob REST. |

> [!NOTE]
> Para **o Azure Blob Storage**, se quiser garantir que o evento **Microsoft.Storage.BlobCreated** só é acionado quando um Block Blob está completamente comprometido, filtre o evento para as `CopyBlob` chamadas , e REST `PutBlob` `PutBlockList` API. Estas chamadas API desencadeiam o evento **Microsoft.Storage.BlobCreated** apenas depois de os dados estiverem totalmente comprometidos com uma Blob de Bloco. Para aprender a criar um filtro, consulte [eventos de filtro para grelha de eventos.](./how-to-filter-events.md)

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista dos eventos para Azure Data Lake Storage Gen 2 REST APIs

Estes eventos são desencadeados se você ativar um espaço hierárquico de nomes na conta de armazenamento, e os clientes usam Azure Data Lake Storage Gen2 REST APIs. Para obter mais informações sobre a Azure Data Lake Storage Gen2, consulte [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Nome do evento|Descrição|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Acionado quando uma bolha é criada ou substituída. <br>Especificamente, este evento é desencadeado quando os clientes usam o `CreateFile` e `FlushWithClose` operações que estão disponíveis na API de Armazenamento de Data Lake De Azure. |
|**Microsoft.Storage.BlobDeleted** |Acionado quando uma bolha é apagada. <br>Especificamente, este evento também é desencadeado quando os clientes ligam para a `DeleteFile` operação que está disponível na API de Armazenamento de Data Lake De Azure. |
|**Microsoft.Storage.BlobRenamed**|Desencadeado quando uma bolha é renomeada. <br>Especificamente, este evento é desencadeado quando os clientes usam a `RenameFile` operação que está disponível na API de Armazenamento de Data Lake De Azure.|
|**Microsoft.Storage.DirectoryCreated**|Desencadeado quando um diretório é criado. <br>Especificamente, este evento é desencadeado quando os clientes usam a `CreateDirectory` operação que está disponível na API de Armazenamento de Data Lake De Azure.|
|**Microsoft.Storage.DirectoryRenamed**|Desencadeado quando um diretório é renomeado. <br>Especificamente, este evento é desencadeado quando os clientes usam a `RenameDirectory` operação que está disponível na API de Armazenamento de Data Lake De Azure.|
|**Microsoft.Storage.DirectoryDeleted**|Acionado quando um diretório é apagado. <br>Especificamente, este evento é desencadeado quando os clientes usam a `DeleteDirectory` operação que está disponível na API de Armazenamento de Data Lake De Azure.|

> [!NOTE]
> Para **o Azure Data Lake Storage Gen2**, se quiser garantir que o evento **Microsoft.Storage.BlobCreated** é acionado apenas quando um Block Blob estiver completamente comprometido, filtre o evento para a `FlushWithClose` chamada REST API. Esta chamada API dispara o evento **Microsoft.Storage.BlobCreated** apenas depois de os dados estiverem totalmente comprometidos com uma Blob de Bloco. Para aprender a criar um filtro, consulte [eventos de filtro para grelha de eventos.](./how-to-filter-events.md)

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>O conteúdo de uma resposta do evento

Quando um evento é desencadeado, o serviço De Grelha de Eventos envia dados sobre esse evento para subscrever o ponto final.

Esta secção contém um exemplo de como seriam esses dados para cada evento de armazenamento de bolhas.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated evento

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

Se a conta de armazenamento blob tiver um espaço hierárquico de nomes, os dados são semelhantes ao exemplo anterior, com exceção destas alterações:

* A `dataVersion` chave está definida para um valor de `2` .

* A `data.api` chave está definida para a corda ou . `CreateFile` `FlushWithClose` .

* A `contentOffset` chave está incluída no conjunto de dados.

> [!NOTE]
> Se as aplicações utilizarem `PutBlockList` a operação para enviar uma nova bolha para a conta, os dados não contêm estas alterações.

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

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted evento

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

Se a conta de armazenamento blob tiver um espaço hierárquico de nomes, os dados são semelhantes ao exemplo anterior, com exceção destas alterações:

* A `dataVersion` chave está definida para um valor de `2` .

* A `data.api` chave está definida para a `DeleteFile` corda.

* A `url` chave contém o `dfs.core.windows.net` caminho.

> [!NOTE]
> Se as aplicações utilizarem `DeleteBlob` a operação para eliminar uma bolha da conta, os dados não contêm estas alterações.

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

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed evento

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

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.Evento Decretado

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.Evento Desorenado

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted

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
| tópico | string | Caminho completo de recursos para a fonte do evento. Este campo não é escrito. O Event Grid fornece este valor. |
| subject | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| dados | objeto | Dados do evento de armazenamento de bolhas. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| api | string | A operação que desencadeou o evento. |
| clienteRequestId | string | um ID de pedido fornecido pelo cliente para a operação de API de armazenamento. Este ID pode ser usado para correlacionar com os registos de diagnóstico do Azure Storage utilizando o campo "cliente-pedido-id" nos registos, e pode ser fornecido em pedidos do cliente usando o cabeçalho "x-ms-cliente-pedido-id". Ver [Formato de Registo](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID de pedido gerado pelo serviço para a operação de API de armazenamento. Pode ser usado para correlacionar com os registos de diagnóstico de armazenamento Azure usando o campo "solicit-id-header" nos registos e é devolvido do início da chamada API no cabeçalho 'x-ms-request-id'. Ver [Formato de Registo](/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | O valor que pode usar para executar operações condicionalmente. |
| conteúdoType | string | O tipo de conteúdo especificado para a bolha. |
| contentAver | número inteiro | O tamanho da bolha em bytes. |
| BlobType | string | O tipo de bolha. Os valores válidos são "BlockBlob" ou "PageBlob". |
| conjunto de conteúdos | número | A compensação em bytes de uma operação de escrita realizada no ponto em que a aplicação de desencadeamento de eventos completou a escrita para o ficheiro. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço hierárquico de nomes.|
| destinationUrl |string | A url do ficheiro que existirá após a conclusão da operação. Por exemplo, se um ficheiro for renomeado, a `destinationUrl` propriedade contém o url do nome do novo ficheiro. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço hierárquico de nomes.|
| fonteUrl |string | A url do ficheiro que existe antes da operação ser feita. Por exemplo, se um ficheiro for renomeado, `sourceUrl` contém o url do nome original do ficheiro antes da operação do renome. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço hierárquico de nomes. |
| url | string | O caminho para a bolha. <br>Se o cliente utilizar uma API Blob REST, então o url tem esta estrutura: `<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>` . <br>Se o cliente utilizar uma API de Armazenamento de Data Lake, então o url tem esta estrutura: `<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>` . |
| recursivo | string | `True` executar a operação em todas as diretórios infantis; caso `False` contrário. <br>Aparece apenas para eventos desencadeados em contas de armazenamento blob que têm um espaço hierárquico de nomes. |
| sequenciador | string | Um valor de corda opaco que representa a sequência lógica de eventos para qualquer nome de bolha em particular.  Os utilizadores podem usar a comparação de cordas padrão para entender a sequência relativa de dois eventos no mesmo nome blob. |
| armazenamentoDiagnósticos | objeto | Dados de diagnóstico ocasionalmente incluídos pelo serviço de Armazenamento Azure. Quando presente, deve ser ignorado pelos consumidores de eventos. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título  |Descrição  |
|---------|---------|
| [Quickstart: encaminhe os eventos de armazenamento blob para um ponto final personalizado da Web com Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de armazenamento de bolhas para um WebHook. |
| [Quickstart: encaminhe os eventos de armazenamento blob para um ponto final personalizado da Web com PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure PowerShell para enviar eventos de armazenamento de bolhas para um WebHook. |
| [Quickstart: criar e encaminhar eventos de armazenamento Blob com o portal Azure](blob-event-quickstart-portal.md) | Mostra como usar o portal para enviar eventos de armazenamento de bolhas para um WebHook. |
| [Azure CLI: subscreva eventos para uma conta de armazenamento Blob](./scripts/event-grid-cli-blob.md) | Guião de amostra que subscreve o evento para uma conta de armazenamento Blob. Envia o evento para um WebHook. |
| [PowerShell: subscreva eventos para uma conta de armazenamento Blob](./scripts/event-grid-powershell-blob.md) | Guião de amostra que subscreve o evento para uma conta de armazenamento Blob. Envia o evento para um WebHook. |
| [Modelo de gestor de recursos: Criar armazenamento e subscrição blob](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Implementa uma conta de armazenamento de Blobs do Azure e subscreve eventos para essa conta de armazenamento. Envia eventos para um WebHook. |
| [Visão geral: reagir a eventos de armazenamento blob](../storage/blobs/storage-blob-event-overview.md) | Visão geral da integração do armazenamento blob com grade de eventos. |

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grade de Eventos?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição da Azure Event Grid, consulte [o esquema de subscrição da Event Grid](subscription-creation-schema.md).
* Para uma introdução ao trabalho com eventos de armazenamento de bolhas, consulte [eventos de armazenamento Route Blob - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 

---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 938f55ae0ba911ea3a97cd49e6424bf8aaefdc76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381698"
---
### <a name="default"></a>Predefinição

Pode utilizar os seguintes tipos de parâmetros para a ligação da entrada do blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Requer uma ligação "inout" `direction` em *function.jsnuma* biblioteca de `FileAccess.ReadWrite` classe C.

Se tentar ligar-se a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

A ligação `string` ou só é recomendada se o tamanho da bolha for `Byte[]` pequeno, uma vez que todo o conteúdo da bolha é carregado na memória. Geralmente, é preferível usar um `Stream` ou `CloudBlockBlob` tipo. Para mais informações, consulte [Concurrency e utilização da memória](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) no início deste artigo.

### <a name="additional-types"></a>Tipos adicionais

As aplicações que utilizam a [versão 5.0.0 ou superior da extensão de Armazenamento](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) também podem utilizar tipos do [Azure SDK para .NET](/dotnet/api/overview/azure/storage.blobs-readme). Esta versão deixa cair o suporte para o legado `CloudBlobContainer` , , , , , e tipos a favor dos `CloudBlobDirectory` `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` `CloudAppendBlob` seguintes tipos:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [ApendendeBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> Requer uma ligação "inout" `direction` em *function.jsnuma* biblioteca de `FileAccess.ReadWrite` classe C.

Por exemplo, utilizando estes tipos, consulte [o repositório GitHub para a extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples).

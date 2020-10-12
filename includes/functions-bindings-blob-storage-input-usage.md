---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77202128"
---
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

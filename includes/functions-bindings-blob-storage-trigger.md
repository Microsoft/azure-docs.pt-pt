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
ms.openlocfilehash: 4a879c4041fe317955a07eda9dd8a3ef9f542275
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998851"
---
Pode utilizar os seguintes tipos de parâmetros para a bolha de desencadeamento:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Requer uma ligação "inout" `direction` em *function.jsnuma* biblioteca de `FileAccess.ReadWrite` classe C.

Se tentar ligar-se a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Ligação a `string` , ou só é recomendado se o tamanho do `Byte[]` blob for pequeno, uma vez que todo o conteúdo da bolha é carregado na memória. Geralmente, é preferível usar um `Stream` ou `CloudBlockBlob` tipo. Para mais informações, consulte [Concurrency e utilização da memória](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) mais tarde neste artigo.
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
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202128"
---
Pode utilizar os seguintes tipos de parâmetros para a ligação de entrada blob:

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

<sup>1</sup> Requer `direction` de ligação "inout" em *função.json* ou `FileAccess.ReadWrite` numa C# biblioteca de classes.

Se tentar ligar-se a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

A ligação a `string` ou `Byte[]` só é recomendada se o tamanho da bolha for pequeno, uma vez que todo o conteúdo da bolha é carregado na memória. Geralmente, é preferível utilizar um tipo `Stream` ou `CloudBlockBlob`. Para mais informações, consulte [a Concurrency e](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) o uso da memória no início deste artigo.

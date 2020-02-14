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
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202102"
---
Pode ligar-se aos seguintes tipos para escrever bolhas:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> Requer `direction` de encadernação "in" em C# *função.json* ou `FileAccess.Read` numa biblioteca de classes. No entanto, pode utilizar o objeto do recipiente que o tempo de execução proporciona para escrevê-lo, como o upload de bolhas para o recipiente.

<sup>2</sup> Requer `direction` de encadernação "inout" em C# *função.json* ou `FileAccess.ReadWrite` numa biblioteca de classes.

Se tentar ligar-se a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Em funções de sincronização, utilize o valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

A ligação a `string` ou `Byte[]` só é recomendada se o tamanho da bolha for pequeno, uma vez que todo o conteúdo da bolha é carregado na memória. Geralmente, é preferível utilizar um tipo `Stream` ou `CloudBlockBlob`. Para mais informações, consulte [a Concurrency e](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) o uso da memória no início deste artigo.

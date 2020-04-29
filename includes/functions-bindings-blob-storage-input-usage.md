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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
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

<sup>1</sup> Requer encadernação `direction` "inout" em `FileAccess.ReadWrite` *função.json* ou numa biblioteca da classe C#.

Se tentar ligar-se a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

A `string` ligação ou `Byte[]` apenas recomendada se o tamanho da bolha for pequeno, uma vez que todo o conteúdo da bolha é carregado na memória. Geralmente, é preferível utilizar `Stream` `CloudBlockBlob` um ou tipo. Para mais informações, consulte [a Concurrency e](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) o uso da memória no início deste artigo.

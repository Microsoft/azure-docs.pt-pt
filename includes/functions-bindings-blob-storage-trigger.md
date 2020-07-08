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
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77202115"
---
Pode utilizar os seguintes tipos de parâmetros para a bolha de desencadeamento:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Um POCO serializável como JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Requer uma ligação "inout" `direction` em *function.jsnuma* biblioteca de `FileAccess.ReadWrite` classe C.

Se tentar ligar-se a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

A ligação a `string` `Byte[]` , ou POCO só é recomendada se o tamanho do blob for pequeno, uma vez que todo o conteúdo da bolha é carregado na memória. Geralmente, é preferível usar um `Stream` ou `CloudBlockBlob` tipo. Para mais informações, consulte [Concurrency e utilização da memória](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) mais tarde neste artigo.
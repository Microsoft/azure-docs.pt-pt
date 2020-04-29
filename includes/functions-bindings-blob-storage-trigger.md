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
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
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

<sup>1</sup> Requer encadernação `direction` "inout" em `FileAccess.ReadWrite` *função.json* ou numa biblioteca da classe C#.

Se tentar ligar-se a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

A `string`ligação a , `Byte[]`ou POCO só é recomendada se o tamanho da bolha for pequeno, uma vez que todo o conteúdo blob é carregado na memória. Geralmente, é preferível utilizar `Stream` `CloudBlockBlob` um ou tipo. Para mais informações, consulte [a Concurrency e](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) o uso da memória mais tarde neste artigo.
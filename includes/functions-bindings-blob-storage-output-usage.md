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
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642100"
---
Você pode associar aos seguintes tipos para gravar BLOBs:

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

<sup>1</sup> requer Associação `direction` "in" em *Function. JSON* ou `FileAccess.Read` em uma C# biblioteca de classes. No entanto, você pode usar o objeto de contêiner que o tempo de execução fornece para realizar operações de gravação, como carregar BLOBs para o contêiner.

<sup>2</sup> requer Associação `direction` "Inout" em *Function. JSON* ou `FileAccess.ReadWrite` em uma C# biblioteca de classes.

Se você tentar se associar a um dos tipos de SDK de armazenamento e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Nas funções assíncronas, utilize o valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

A associação `string` a `Byte[]` ou é recomendada somente se o tamanho do blob for pequeno, pois todo o conteúdo do blob será carregado na memória. Em geral, é preferível usar um `Stream` tipo `CloudBlockBlob` ou. Para obter mais informações, consulte [simultaneidade e uso de memória](#trigger---concurrency-and-memory-usage) anteriormente neste artigo.
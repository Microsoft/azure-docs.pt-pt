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
ms.openlocfilehash: 557e34e8a6bddd36a92b0e212bda3609baa14407
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642165"
---
Você pode usar os seguintes tipos de parâmetro para o blob de gatilho:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Um POCO serializável como JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> requer Associação `direction` "Inout" em *Function. JSON* ou `FileAccess.ReadWrite` em uma C# biblioteca de classes.

Se você tentar se associar a um dos tipos de SDK de armazenamento e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

A associação `string`a `Byte[]`, ou poco será recomendada apenas se o tamanho do blob for pequeno, pois todo o conteúdo do blob será carregado na memória. Em geral, é preferível usar um `Stream` tipo `CloudBlockBlob` ou. Para obter mais informações, consulte [simultaneidade e uso de memória](#trigger---concurrency-and-memory-usage) mais adiante neste artigo.
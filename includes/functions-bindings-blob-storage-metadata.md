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
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455923"
---
O gatilho blob fornece várias propriedades de metadados. Estas propriedades podem ser usadas como parte de expressões de ligação em outras encadernações ou como parâmetros no seu código. Estes valores têm a mesma semântica que o tipo [CloudBlob.](/dotnet/api/microsoft.azure.storage.blob.cloudblob)

|Propriedade  |Tipo  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|O caminho para a bolha de desencadeamento.|
|`Uri`|`System.Uri`|O URI da bolha para a localização primária.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|As propriedades do sistema da bolha. |
|`Metadata` |`IDictionary<string,string>`|Os metadados definidos pelo utilizador para a bolha.|

Por exemplo, os seguintes exemplos de script C# e JavaScript registam o caminho para a bolha de desencadeamento, incluindo o recipiente:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
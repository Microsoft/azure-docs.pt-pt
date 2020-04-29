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
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "69642139"
---
O gatilho blob fornece várias propriedades de metadados. Estas propriedades podem ser usadas como parte de expressões de ligação noutras encadernações ou como parâmetros no seu código. Estes valores têm a mesma semântica que o tipo [CloudBlob.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet)

|Propriedade  |Tipo  |Descrição  |
|---------|---------|---------|
|`BlobTrigger`|`string`|O caminho para a bolha de desencadeamento.|
|`Uri`|`System.Uri`|O URI da bolha para a localização primária.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|As propriedades do sistema da bolha. |
|`Metadata` |`IDictionary<string,string>`|Os metadados definidos pelo utilizador para a bolha.|

Por exemplo, os exemplos de script C# e JavaScript slog e JavaScript registam o caminho para a bolha de desencadeamento, incluindo o recipiente:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

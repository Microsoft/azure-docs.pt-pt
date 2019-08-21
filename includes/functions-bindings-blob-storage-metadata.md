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
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642139"
---
O gatilho de blob fornece várias propriedades de metadados. Essas propriedades podem ser utilizadas como parte das expressões de associação nas outras associações, ou como parâmetros no seu código. Esses valores têm a mesma semântica que o tipo [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) .

|Propriedade  |Tipo  |Descrição  |
|---------|---------|---------|
|`BlobTrigger`|`string`|O caminho para o blob de gatilho.|
|`Uri`|`System.Uri`|O URI do blob para o local primário.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|As propriedades do sistema do blob. |
|`Metadata` |`IDictionary<string,string>`|Os metadados definidos pelo usuário para o blob.|

Por exemplo, os exemplos C# de script e JavaScript a seguir registram em log o caminho para o blob de disparo, incluindo o contêiner:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

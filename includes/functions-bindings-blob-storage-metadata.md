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
ms.openlocfilehash: f4e6d5fb41769544b7be0f689447364988d0380d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998850"
---
O gatilho blob fornece várias propriedades de metadados. Estas propriedades podem ser usadas como parte de expressões de ligação em outras encadernações ou como parâmetros no seu código. Estes valores têm a mesma semântica que o tipo [CloudBlob.](/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet)

|Propriedade  |Tipo  |Descrição  |
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
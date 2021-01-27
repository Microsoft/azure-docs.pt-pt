---
title: Comparação de amostras de migração de Serviços de Comunicação Social v2 a V3
description: Um conjunto de amostras para ajudá-lo a comparar as diferenças de código entre a Azure Media Services v2 e v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 640b9b40295ae9b9aea865f7b6159da6ff4a3251
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898312"
---
# <a name="media-services-migration-code-sample-comparison"></a>Comparação de amostra de código de migração de serviços de mídia

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

Podes usar algumas das nossas amostras de código para comparar a forma como as coisas são feitas entre os SDKs.

## <a name="samples-for-comparison"></a>Amostras para comparação

Segue-se uma lista de amostras para comparação entre v2 e v3 para cenários comuns.

|Scenario|v2 API|v3 API|
|---|---|---|
|Criar um ativo e fazer upload de um ficheiro |[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Submeter um emprego|[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Mostra como primeiro criar uma Transform e depois submeter um Job.|
|Publique um ativo com encriptação AES |1. Criar `ContentKeyAuthorizationPolicyOption`<br/>2. Criar `ContentKeyAuthorizationPolicy`<br/>3. Criar `AssetDeliveryPolicy`<br/>4. Criar `Asset` e carregar conteúdo OU submeter e `Job` utilizar `OutputAsset`<br/>5. `AssetDeliveryPolicy` Associar-se com `Asset`<br/>6. Criar `ContentKey`<br/>7. Anexar `ContentKey` a `Asset`<br/>8. Criar `AccessPolicy`<br/>9. Criar `Locator`<br/><br/>[v2 .NET exemplo](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Criar `ContentKeyPolicy`<br/>2. Criar `Asset`<br/>3. Carregar conteúdo ou utilizar `Asset` como `JobOutput`<br/>4. Criar `StreamingLocator`<br/><br/>[v3 .NET exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Obter detalhes de emprego e gerir empregos |[Gerir empregos com v2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Gerir empregos com v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]

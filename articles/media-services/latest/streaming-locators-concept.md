---
title: Localizadores de streaming nos Serviços De Mídia Azure [ Microsoft Docs
description: Este artigo dá uma explicação do que são os Localizadores de Streaming e como são usados pela Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 3a9568e1a0307cd1713c511ef42c065424306548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302887"
---
# <a name="streaming-locators"></a>Localizadores de Transmissão em Fluxo

Para fazer vídeos no Recurso de saída disponível para a reprodução em clientes, tem de criar um [Localizador de Transmissões em Fluxo](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, criar os URLs das transmissões. Para construir um URL, você precisa concatenar o nome de anfitrião do Streaming Endpoint e o caminho do localizador de streaming. Para um exemplo de .NET, veja [Get a Streaming Locator](stream-files-tutorial-with-api.md#get-a-streaming-locator) (Obter um Localizador de Transmissão em Fluxo).

O processo de criação de um Localizador de **Streaming** chama-se publicação. Por predefinição, o Localizador de **Streaming** é válido imediatamente após a efetuação das chamadas API, e dura até que seja eliminado, a menos que configure os tempos de início e fim opcionais. 

Ao criar um Localizador de **Streaming,** deve especificar um nome **de Ativo** e um nome de **Política de Streaming.** Para obter mais informações, consulte os seguintes tópicos:

* [Recursos](assets-concept.md)
* [Políticas de Transmissão em Fluxo](streaming-policy-concept.md)
* [Políticas de Chave de Conteúdo](content-key-policy-concept.md)

Também pode especificar o tempo de início e fim no seu Localizador de Streaming, que só permitirá que o seu utilizador reveja o conteúdo entre estes tempos (por exemplo, entre 5/1/2019 e 5/5/2019).  

## <a name="considerations"></a>Considerações

* **Localizadores** de streaming não são atualizáveis. 
* As propriedades dos **Localizadores** de Streaming que são do tipo Datetime estão sempre em formato UTC.
* Deverá conceber um conjunto limitado de políticas para a sua conta de Serviço de Media e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções. Para mais informações, consulte [Quotas e limitações.](limits-quotas-constraints.md)

## <a name="create-streaming-locators"></a>Criar localizadores de streaming  

### <a name="not-encrypted"></a>Não encriptado

Se pretender transmitir o seu ficheiro de forma clara (não encriptada), delineie a política de streaming clara predefinida: para 'Predefined_ClearStreamingOnly' (em .NET, pode utilizar o PredefinidoStreamingPolicy.ClearStreamingOnly enum).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Encriptados 

Se precisar de encriptar o seu conteúdo com a encriptação CENC, detete a sua política para 'Predefined_MultiDrmCencStreaming'. A encriptação Widevine será aplicada a um fluxo DASH e PlayReady para Smooth. A chave será entregue a um cliente de reprodução com base nas licenças de DRM configuradas.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Se também quiser encriptar o seu fluxo HLS com CBCS (FairPlay), utilize 'Predefined_MultiDrmStreaming'.

> [!NOTE]
> A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Filtros associados com localizadores de streaming

Ver [Filtros: associar-se a localizadores](filters-concept.md#associating-filters-with-streaming-locator)de streaming .

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrar, encomendar, página streaming entidades localizadores

Ver [Filtragem, encomenda, paging de entidades dos Serviços de Media.](entities-overview.md)

## <a name="list-streaming-locators-by-asset-name"></a>Lista de localizadores de streaming por nome de ativo

Para obter localizadores de streaming com base no nome de Ativo associado, utilize as seguintes operações:

|Idioma|API|
|---|---|
|REST|[localizadores de liststreaming](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams lista de sites de streaming-localizadores](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listaStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Consulte também

* [Recursos](assets-concept.md)
* [Políticas de Transmissão em Fluxo](streaming-policy-concept.md)
* [Políticas de Chave de Conteúdo](content-key-policy-concept.md)
* [Tutorial: Upload, codificação e streaming de vídeos usando .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Passos seguintes

[Como criar um localizador de streaming e construir URLs](create-streaming-locator-build-url.md)

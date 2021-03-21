---
title: Localizadores de streaming em Azure Media Services
description: Este artigo dá uma explicação sobre o que são os Localizadores de Streaming e como são usados pela Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 8f7357644639e1bdba07b9ad44533582fc66b600
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456081"
---
# <a name="streaming-locators"></a>Localizadores de Transmissão em Fluxo

Para fazer vídeos no Recurso de saída disponível para a reprodução em clientes, tem de criar um [Localizador de Transmissões em Fluxo](/rest/api/media/streaminglocators) e, em seguida, criar os URLs das transmissões. Para construir um URL, é necessário concatenar o nome do anfitrião streaming Endpoint e o caminho do localizador de streaming. Para um exemplo de .NET, veja [Get a Streaming Locator](stream-files-tutorial-with-api.md#get-a-streaming-locator) (Obter um Localizador de Transmissão em Fluxo).

O processo de criação de um **Localizador de Streaming** chama-se publicação. Por predefinição, o **Localizador de Streaming** é válido imediatamente após a edição da API e dura até ser eliminado, a menos que configuure os tempos de início e fim opcionais. 

Ao criar um **localizador de streaming,** tem de especificar um nome **de Ativo** e um nome **de Política de Streaming.** Para obter mais informações, consulte os seguintes tópicos:

* [Recursos](assets-concept.md)
* [Políticas de streaming](streaming-policy-concept.md)
* [Políticas-chave de conteúdo](content-key-policy-concept.md)

Também pode especificar o tempo de início e fim no seu Localizador de Streaming, que só permitirá ao seu utilizador reproduzir o conteúdo entre estes tempos (por exemplo, entre 5/1/1/2019 a 5/5/2019).  

## <a name="considerations"></a>Considerações

* **Os localizadores de streaming** não sãoáveis. 
* As propriedades dos **localizadores** de streaming que são do tipo Datetime estão sempre em formato UTC.
* Deve conceber um conjunto limitado de políticas para a sua conta de Media Service e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções. Para mais informações, consulte [Quotas e limites.](limits-quotas-constraints.md)

## <a name="create-streaming-locators"></a>Criar localizadores de streaming  

### <a name="not-encrypted"></a>Não encriptado

Se pretender transmitir o seu ficheiro de forma clara (não encriptada), defina a política de streaming clara predefinida: para "Predefined_ClearStreamingOnly" (em .NET, pode utilizar a PredefinedStreamingPoliingPolicy.ClearStreamingOnly enum).

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

Se precisar de encriptar o seu conteúdo com a encriptação CENC, desavere a sua política para "Predefined_MultiDrmCencStreaming". A encriptação Widevine será aplicada a um stream DASH e PlayReady para Smooth. A chave será entregue a um cliente de reprodução com base nas licenças de DRM configuradas.

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

Se também pretender encriptar o seu fluxo HLS com o CBCS (FairPlay), utilize 'Predefined_MultiDrmStreaming'.

> [!NOTE]
> Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associar filtros com localizadores de streaming

Ver [Filtros: associar-se aos localizadores de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrar, encomendar, página streaming de entidades localizadores

Ver [Filtragem, encomenda, paging de entidades de Serviços de Comunicação](entities-overview.md)Social.

## <a name="list-streaming-locators-by-asset-name"></a>Lista de localizadores de streaming por nome de ativo

Para obter localizadores de streaming com base no nome do Ativo associado, utilize as seguintes operações:

|Linguagem|API|
|---|---|
|REST|[liststreaminglocadores](/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams lista de ativos-streaming-localizadores](/cli/azure/ams/asset#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[Gestor de ActivosStreamingLotor](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Ver também

* [Recursos](assets-concept.md)
* [Políticas de streaming](streaming-policy-concept.md)
* [Políticas-chave de conteúdo](content-key-policy-concept.md)
* [Tutorial: Carregar, codificar e transmitir vídeos usando .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Passos seguintes

[Como criar um localizador de streaming e construir URLs](create-streaming-locator-build-url.md)

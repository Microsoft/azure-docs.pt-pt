---
title: Criar filtros com a Azure Media Services v3 .NET SDK
description: Este tópico descreve como criar filtros para que o seu cliente possa usá-los para transmitir secções específicas de um fluxo. Os Serviços de Mídia v3 .NET SDK criam manifestos dinâmicos para alcançar este streaming seletivo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9f54a505d63660fb7c97b3226df4c232e48125e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90527365"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Criar filtros com Serviços de Mídia .NET SDK

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao entregar o seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo a pedido) o seu cliente poderá necessitar de mais flexibilidade do que o descrito no ficheiro manifesto do ativo padrão. O Azure Media Services permite-lhe definir filtros de conta e filtros de ativos para o seu conteúdo. 

Para uma descrição detalhada desta característica e cenários onde é utilizado, consulte [Manifestos Dinâmicos](filters-dynamic-manifest-overview.md) e [Filtros.](filters-concept.md)

Este tópico mostra como utilizar os Serviços de Comunicação Social .NET SDK para definir um filtro para um ativo Video on Demand e criar [Filtros de Conta](/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) e [Filtros de Ativos](/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Certifique-se de rever a [apresentaçãoTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

- Rever [filtros e manifestos dinâmicos.](filters-dynamic-manifest-overview.md)
- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social. 
- Obtenha informações necessárias para aceder a [APIs](./access-api-howto.md)
- [Reveja upload, codificação e stream usando Azure Media Services](stream-files-tutorial-with-api.md) para ver como começar a usar [.NET SDK](stream-files-tutorial-with-api.md#start-using-media-services-apis-with-net-sdk)

## <a name="define-a-filter"></a>Definir um filtro  

Em .NET, configura seleções de faixas com as classes [FilterTrackSelection](/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) e [FilterTrackPropertyCondition.](/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 

O código que se segue define um filtro que inclui quaisquer faixas de áudio que sejam EC-3 e quaisquer faixas de vídeo que tenham bitrate na gama 0-100000.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Criar filtros de conta

O código que se segue mostra como utilizar .NET para criar um filtro de conta que inclua todas as seleções de faixas [definidas acima.](#define-a-filter) 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Criar filtros de ativos

O código que se segue mostra como utilizar .NET para criar um filtro de ativos que inclua todas as seleções de faixas [definidas acima.](#define-a-filter) 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros com localizador de streaming

Pode especificar uma lista de filtros de ativos ou de conta, que se aplicaria ao seu Localizador de Streaming. O [Dynamic Packager (Streaming Endpoint)](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os que o seu cliente especifica no URL. Esta combinação gera um [Manifesto Dinâmico,](filters-dynamic-manifest-overview.md)que é baseado em filtros nos filtros URL + que especifica no Localizador de Streaming. Recomendamos que utilize esta função se pretender aplicar filtros, mas não pretende expor os nomes dos filtros no URL.

O código C# que se segue mostra como criar um localizador de streaming e especificar `StreamingLocator.Filters` . Esta é uma propriedade opcional que leva um `IList<string>` nome de filtro.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Fluxo usando filtros

Assim que definir filtros, os seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados em protocolos de streaming de bitrate adaptativo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passos seguintes

[Stream vídeos](stream-files-tutorial-with-api.md) 

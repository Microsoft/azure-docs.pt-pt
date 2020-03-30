---
title: Criação de filtros com Azure Media Services v3 .NET SDK
description: Este tópico descreve como criar filtros para que o seu cliente possa usá-los para transmitir secções específicas de um fluxo. A Media Services cria manifestos dinâmicos para alcançar este streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: ef04b1b7b5030189482e89e26e4565397cbdd7c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779251"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Criar filtros com Media Services .NET SDK

Ao entregar o seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo a pedido) o seu cliente pode precisar de mais flexibilidade do que o descrito no ficheiro manifesto do ativo predefinido. O Azure Media Services permite-lhe definir filtros de conta e filtros de ativos para o seu conteúdo. 

Para uma descrição detalhada desta funcionalidade e cenários onde é utilizada, consulte [Manifestos dinâmicos](filters-dynamic-manifest-overview.md) e [filtros](filters-concept.md).

Este tópico mostra como usar os Media Services .NET SDK para definir um filtro para um ativo de vídeo a pedido e criar [filtros](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) de conta e [filtros de ativos](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Certifique-se de rever a [apresentaçãoTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

- Rever [Filtros e manifestos dinâmicos.](filters-dynamic-manifest-overview.md)
- [Criar uma conta de Media Services.](create-account-cli-how-to.md) Lembre-se do nome do grupo de recursos e do nome da conta Media Services. 
- Obtenha informações necessárias para aceder a [APIs](access-api-cli-how-to.md)
- [Reveja o upload, codificação e stream usando o Azure Media Services](stream-files-tutorial-with-api.md) para ver como começar a [usar .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Defina um filtro  

Em .NET, configura as seleções de faixas com as classes [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) e [FilterTrackPropertyCondition.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) 

O código que se segue define um filtro que inclui quaisquer faixas áudio que sejam EC-3 e quaisquer faixas de vídeo que tenham bitrate na gama 0-1000000.

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

O código que se segue mostra como utilizar .NET para criar um filtro de conta que inclua todas as seleções de [faixas definidas acima .](#define-a-filter) 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Criar filtros de ativos

O código que se segue mostra como utilizar .NET para criar um filtro de ativos que inclua todas as seleções de [faixas definidas acima .](#define-a-filter) 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Filtros associados com localizador de streaming

Pode especificar uma lista de filtros de ativos ou conta, que se aplicaria ao seu Localizador de Streaming. O [Pacote Dinâmico (Streaming Endpoint)](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os especificados pelo seu cliente no URL. Esta combinação gera um [Manifesto Dinâmico,](filters-dynamic-manifest-overview.md)que é baseado em filtros no URL + filtros que especifica no Localizador de Streaming. Recomendamos que utilize esta funcionalidade se pretender aplicar filtros, mas não pretende expor os nomes dos filtros no URL.

O código C# seguinte mostra como criar `StreamingLocator.Filters`um Localizador de Streaming e especificar . Esta é uma propriedade `IList<string>` opcional que leva um dos nomes de filtro.

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

Uma vez definidofiltros, os seus clientes podem usá-los no URL de streaming. Os filtros podem ser aplicados em protocolos de streaming de bitrate adaptativo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela que se segue mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passos seguintes

[Stream vídeos](stream-files-tutorial-with-api.md) 



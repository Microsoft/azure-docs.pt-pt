---
title: Criar Filtros com o SDK .NET dos Serviços de Multimédia do Azure
description: Este tópico descreve como criar filtros para que o cliente possa utilizar secções específicas do fluxo de um fluxo. Serviços de multimédia cria os manifestos dinâmicos para alcançar este seletiva de transmissão em fluxo.
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
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 8c786f46308848c6b9182453510744942a8eb9e8
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472423"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Criar filtros com o SDK .NET dos Media Services

Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido) o cliente poderá ter mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Serviços de multimédia do Azure permite-lhe definir os filtros de conta e filtros de recurso para o seu conteúdo. Para obter mais informações, consulte [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).

Este tópico mostra como utilizar o SDK .NET dos Media Services para definir um filtro para ver um vídeo num recurso a pedido e crie [filtros de conta](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) e [Asset filtros](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

## <a name="prerequisites"></a>Pré-requisitos 

- Revisão [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).
- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 
- Obtenha informações necessárias para [aceder a APIs](access-api-cli-how-to.md)
- Revisão [carregar, codificar e transmitir em fluxo através dos serviços de multimédia do Azure](stream-files-tutorial-with-api.md) para ver como [começar a utilizar o .NET SDK](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Definir um filtro  

No .NET, configurou as seleções de controle com [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) e [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) classes. 

O código a seguir define um filtro que inclui qualquer faixas de áudio são EC 3 e nenhum Roteiro de vídeo com velocidade de transmissão no 0-1000000 intervalo.

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

O código seguinte mostra como utilizar o .NET para criar um filtro de conta, que inclui todas as seleções de faixa [definido acima](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Criar filtros de elemento

O código seguinte mostra como utilizar o .NET para criar um filtro de elemento que inclui todas as seleções de faixa [definido acima](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros localizador de transmissão em fluxo

Pode especificar uma lista dos filtros de ativo ou a conta, que seria aplicada para o localizador de transmissão em fluxo. O [packager dinâmico (transmissão em fluxo o ponto de extremidade)](dynamic-packaging-overview.md) aplica-se esta lista de filtros em conjunto com o seu cliente especifica no URL. Esta combinação gera uma [manifesto dinâmico](filters-dynamic-manifest-overview.md), que se baseia em filtros no URL + filtros que especificar no localizador de transmissão em fluxo. Recomendamos que utilize esta funcionalidade se pretenda aplicar filtros, mas não pretende expor os nomes de filtro no URL.

O seguinte C# código mostra como criar um localizador de transmissão em fluxo e especifique `StreamingLocator.Filters`. Esta é uma propriedade opcional que usa um `IList<string>` dos nomes de filtro.

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
      
## <a name="stream-using-filters"></a>Utilizar os filtros de Stream

Depois de definir filtros, os clientes podem utilizá-los o URL de transmissão em fluxo. Filtros podem ser aplicados a velocidade de transmissão adaptável, protocolos de transmissão em fluxo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela seguinte mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passos Seguintes

[Vídeos do Stream](stream-files-tutorial-with-api.md) 



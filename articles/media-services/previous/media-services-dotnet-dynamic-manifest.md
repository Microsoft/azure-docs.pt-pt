---
title: Criar Filtros com o SDK .NET dos Serviços de Multimédia do Azure
description: Este tópico descreve como criar filtros para que o seu cliente possa usá-los para transmitir secções específicas de um fluxo. A Media Services cria manifestos dinâmicos para alcançar este streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: cenkdin
ms.openlocfilehash: c60b223f91a151bf63cabc5e95816f2545022503
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016595"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Criação de filtros com serviços de mídia .NET SDK 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A partir do lançamento de 2.17, o Media Services permite-lhe definir filtros para os seus ativos. Estes filtros são regras do lado do servidor que permitem que os seus clientes escolham fazer coisas como: reprodução apenas uma secção de um vídeo (em vez de reproduzir todo o vídeo), ou especificar apenas um subconjunto de representações de áudio e vídeo que o dispositivo do seu cliente pode manusear (em vez de todas as representações que estão associadas ao ativo). Esta filtragem dos seus ativos é conseguida através do **Dynamic Manifest**s que são criados a pedido do seu cliente para transmitir um vídeo com base em filtros especificados.

Para obter informações mais detalhadas relacionadas com filtros e manifesto dinâmico, consulte a visão geral dos [manifestos dinâmicos.](media-services-dynamic-manifest-overview.md)

Este artigo mostra como usar os Media Services .NET SDK para criar, atualizar e eliminar filtros. 

Note que se atualizar um filtro, pode demorar até dois minutos para o ponto final de streaming atualizar as regras. Se o conteúdo foi servido utilizando este filtro (e cached em proxies e caches CDN), a atualização deste filtro pode resultar em falhas dos jogadores. Limpe sempre a cache depois de atualizar o filtro. Se esta opção não for possível, considere utilizar um filtro diferente. 

## <a name="types-used-to-create-filters"></a>Tipos usados para criar filtros
Os seguintes tipos são utilizados na criação de filtros: 

* **Filtro iStreaming**.  Este tipo baseia-se no seguinte [filtro](https://docs.microsoft.com/rest/api/media/operations/filter) REST API
* **Filtro istreamingAsset**. Este tipo baseia-se no seguinte REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **ApresentaçãoTimeRange**. Este tipo baseia-se no seguinte REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** e **IFilterTrackPropertyCondition**. Estes tipos baseiam-se nos seguintes REST APIs [FilterTrackSelect e FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Criar/Atualizar/Ler/Eliminar filtros globais
O código que se segue mostra como usar .NET para criar, atualizar, ler e eliminar filtros de ativos.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>Criar/Atualizar/Ler/Eliminar filtros de ativos
O código que se segue mostra como usar .NET para criar, atualizar, ler e eliminar filtros de ativos.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>Construa URLs de streaming que usam filtros
Para obter informações sobre como publicar e entregar os seus ativos, consulte [A Entrega de Conteúdos à Visão Geral](media-services-deliver-content-overview.md)dos Clientes .

Os seguintes exemplos mostram como adicionar filtros aos seus URLs de streaming.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Transmissão em Fluxo Uniforme**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Manifestos dinâmicos visão geral](media-services-dynamic-manifest-overview.md)


---
title: Criação de filtros com Azure Media Services v3 REST API
description: Este tópico descreve como criar filtros para que o seu cliente possa usá-los para transmitir secções específicas de um fluxo. A API dos Serviços de Mídia v3 REST cria manifestos dinâmicos para alcançar este streaming seletivo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c1d7bf933b487c40d571f1912341b5ef771e4e67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90527331"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Criação de filtros com Serviços de Mídia REST API

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao entregar o seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo a pedido) o seu cliente poderá necessitar de mais flexibilidade do que o descrito no ficheiro manifesto do ativo padrão. O Azure Media Services permite-lhe definir filtros de conta e filtros de ativos para o seu conteúdo. 

Para uma descrição detalhada desta característica e cenários onde é utilizado, consulte [Manifestos Dinâmicos](filters-dynamic-manifest-overview.md) e [Filtros.](filters-concept.md)

Este tópico mostra como definir um filtro para um ativo Video on Demand e usar APIs REST para criar [filtros de conta](/rest/api/media/accountfilters) e [filtros de ativos](/rest/api/media/assetfilters). 

> [!NOTE]
> Certifique-se de rever a [apresentaçãoTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

Para completar os passos descritos neste tópico, tem de:

- Rever [filtros e manifestos dinâmicos.](filters-dynamic-manifest-overview.md)
- [Configure Carteiro para Azure Media Services REST Chamadas API](media-rest-apis-with-postman.md).

    Certifique-se de seguir o último passo do tema [Get Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definir um filtro  

Segue-se o exemplo **do corpo request** que define as condições de seleção da faixa que são adicionadas ao manifesto. Este filtro inclui quaisquer faixas de áudio que sejam EC-3 e quaisquer faixas de vídeo que tenham bitrate na gama 0-100000.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Criar filtros de conta

Na coleção do Carteiro que descarregou, selecione **Filtros de Conta** -> **Criar ou atualizar um Filtro de Conta**.

O método de pedido **PUT** HTTP é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selecione o **separador 'Corpo'** e cole o código json que [definiu anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro foi criado.

Para mais informações, consulte [Criar ou atualizar](/rest/api/media/accountfilters/createorupdate). Consulte também os [exemplos JSON para filtros.](/rest/api/media/accountfilters/createorupdate#create-an-account-filter)

## <a name="create-asset-filters"></a>Criar filtros de ativos  

Na coleção "Media Services v3" do Carteiro que descarregou, selecione **Ativos** -> **Criar ou atualizar o Filtro de Ativos.**

O método de pedido **PUT** HTTP é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecione o **separador 'Corpo'** e cole o código json que [definiu anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro de ativos foi criado.

Para obter detalhes sobre como criar ou atualizar filtros de ativos, consulte [Criar ou atualizar](/rest/api/media/assetfilters/createorupdate). Consulte também os [exemplos JSON para filtros.](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter) 

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros com localizador de streaming

Pode especificar uma lista de filtros de ativos ou de conta, que se aplicaria ao seu Localizador de Streaming. O [Dynamic Packager (Streaming Endpoint)](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os que o seu cliente especifica no URL. Esta combinação gera um [Manifesto Dinâmico,](filters-dynamic-manifest-overview.md)que é baseado em filtros nos filtros URL + que especifica no Localizador de Streaming. Recomendamos que utilize esta função se pretender aplicar filtros, mas não pretende expor os nomes dos filtros no URL.

Para criar e associar filtros a um localizador de streaming utilizando REST, utilize os [localizadores de streaming - Crie](/rest/api/media/streaminglocators/create) API e especifique no Corpo de `properties.filters` [Pedidos](/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Fluxo usando filtros

Assim que definir filtros, os seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados em protocolos de streaming de bitrate adaptativo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passos seguintes

[Stream vídeos](stream-files-tutorial-with-rest.md) 

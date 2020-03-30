---
title: Criação de filtros com azure media services v3 REST API
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780339"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Criação de filtros com Media Services REST API

Ao entregar o seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo a pedido) o seu cliente pode precisar de mais flexibilidade do que o descrito no ficheiro manifesto do ativo predefinido. O Azure Media Services permite-lhe definir filtros de conta e filtros de ativos para o seu conteúdo. 

Para uma descrição detalhada desta funcionalidade e cenários onde é utilizada, consulte [Manifestos dinâmicos](filters-dynamic-manifest-overview.md) e [filtros](filters-concept.md).

Este tópico mostra como definir um filtro para um ativo de vídeo a pedido e usar APIs REST para criar [filtros](https://docs.microsoft.com/rest/api/media/accountfilters) de conta e [filtros de ativos](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Certifique-se de rever a [apresentaçãoTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

Para completar os passos descritos neste tópico, tem de:

- Rever [Filtros e manifestos dinâmicos.](filters-dynamic-manifest-overview.md)
- [Configure Postman para Azure Media Services REST API chamadas](media-rest-apis-with-postman.md).

    Certifique-se de seguir o último passo no tópico [Get Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Defina um filtro  

Segue-se o exemplo **do corpo do Pedido** que define as condições de seleção da pista que são adicionadas ao manifesto. Este filtro inclui quaisquer faixas áudio que sejam EC-3 e quaisquer faixas de vídeo que tenham bitrate na gama 0-10000000.

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

Na coleção do Carteiro que descarregou, selecione **Filtros**->de Conta**Criar ou atualizar um Filtro**de Conta .

O método de pedido **PUT** HTTP é semelhante ao seguinte:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selecione o separador **Body** e colhe o código json que [definiu anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro foi criado.

Para mais informações, consulte [Criar ou atualizar](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Consulte também [exemplos jSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Criar filtros de ativos  

Na coleção "Media Services v3" Postman que descarregou, selecione **Assets**->**Create ou atualize**o Filtro de Ativos .

O método de pedido **PUT** HTTP é semelhante ao seguinte:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecione o separador **Body** e colhe o código json que [definiu anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro de ativos foi criado.

Para mais detalhes sobre como criar ou atualizar filtros de ativos, consulte [Criar ou atualizar](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Consulte também [exemplos jSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Filtros associados com localizador de streaming

Pode especificar uma lista de filtros de ativos ou conta, que se aplicaria ao seu Localizador de Streaming. O [Pacote Dinâmico (Streaming Endpoint)](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os especificados pelo seu cliente no URL. Esta combinação gera um [Manifesto Dinâmico,](filters-dynamic-manifest-overview.md)que é baseado em filtros no URL + filtros que especifica no Localizador de Streaming. Recomendamos que utilize esta funcionalidade se pretender aplicar filtros, mas não pretende expor os nomes dos filtros no URL.

Para criar e associar filtros a um localizador de streaming utilizando o REST, utilize os Localizadores de [Streaming - Crie](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API e especifique `properties.filters` no Corpo de [Pedidos](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Fluxo usando filtros

Uma vez definidofiltros, os seus clientes podem usá-los no URL de streaming. Os filtros podem ser aplicados em protocolos de streaming de bitrate adaptativo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela que se segue mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passos seguintes

[Stream vídeos](stream-files-tutorial-with-rest.md) 

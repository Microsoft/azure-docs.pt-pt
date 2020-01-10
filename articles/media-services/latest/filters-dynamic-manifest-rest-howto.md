---
title: Criando filtros com a API REST dos serviços de mídia do Azure v3
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780339"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Criar filtros com o API de REST dos serviços de multimédia

Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido) o cliente poderá ter mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Serviços de multimédia do Azure permite-lhe definir os filtros de conta e filtros de recurso para o seu conteúdo. 

Para obter uma descrição detalhada desse recurso e dos cenários em que ele é usado, consulte manifestos e [filtros](filters-concept.md) [dinâmicos](filters-dynamic-manifest-overview.md) .

Este tópico mostra como definir um filtro para ver um vídeo num recurso a pedido e utilizar REST APIs para criar [filtros de conta](https://docs.microsoft.com/rest/api/media/accountfilters) e [Asset filtros](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Certifique-se de examinar [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir os passos descritos neste tópico, tem de:

- Revisão [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).
- [Configurar o Postman para chamar a API de REST de serviços de multimédia do Azure](media-rest-apis-with-postman.md).

    Certifique-se de seguir a última etapa no tópico [obter token do Azure ad](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definir um filtro  

Segue-se a **corpo do pedido** exemplo que define as condições de seleção de controle que são adicionadas ao manifesto. Esse filtro inclui quaisquer faixas de áudio EC-3 e quaisquer faixas de vídeo com taxa de bits no intervalo de 0-1000000.

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

Coleção do Postman que transferiu, selecione **filtros de conta**->**criar ou atualizar um filtro de conta**.

O **colocar** método de pedido HTTP é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Selecione o **corpo** separador e colar o json de código que [definidas anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro foi criado.

Para obter mais informações, consulte [criar ou atualizar](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Criar filtros de elemento  

Na coleção do "Media Services V3" do postmaster que você baixou, selecione **ativos**->**criar ou atualizar o filtro de ativo**.

O **colocar** método de pedido HTTP é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecione o **corpo** separador e colar o json de código que [definidas anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro de elemento foi criado.

Para obter detalhes sobre como criar ou atualizar os filtros de recurso, consulte [criar ou atualizar](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros ao localizador de streaming

Você pode especificar uma lista de ativos ou filtros de conta, que se aplicariam ao seu localizador de streaming. O [Gerenciador dinâmico (ponto de extremidade de streaming)](dynamic-packaging-overview.md) aplica essa lista de filtros junto com aqueles que seu cliente especifica na URL. Essa combinação gera um [manifesto dinâmico](filters-dynamic-manifest-overview.md), que é baseado em filtros na URL + filtros que você especificar no localizador de streaming. Recomendamos que você use esse recurso se desejar aplicar filtros, mas não quiser expor os nomes de filtro na URL.

Para criar e associar filtros a um localizador de streaming usando REST, use os [localizadores de streaming – criar](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API e especifique `properties.filters` no [corpo da solicitação](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Fluxo usando filtros

Depois de definir filtros, os clientes podem usá-los na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passos seguintes

[Vídeos do Stream](stream-files-tutorial-with-rest.md) 

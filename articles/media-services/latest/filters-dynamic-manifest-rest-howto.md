---
title: REST API dos serviços de criar filtros com o suporte de dados do Azure | Documentos da Microsoft
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
ms.openlocfilehash: 447116267e53f8c4df1e882ca30c6a2e906d314c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67114298"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Criar filtros com o API de REST dos serviços de multimédia

Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido) o cliente poderá ter mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Serviços de multimédia do Azure permite-lhe definir os filtros de conta e filtros de recurso para o seu conteúdo. 

Para obter descrição detalhada desta funcionalidade e cenários em que é utilizada, consulte [manifestos dinâmica](filters-dynamic-manifest-overview.md) e [filtros](filters-concept.md).

Este tópico mostra como definir um filtro para ver um vídeo num recurso a pedido e utilizar REST APIs para criar [filtros de conta](https://docs.microsoft.com/rest/api/media/accountfilters) e [Asset filtros](https://docs.microsoft.com/rest/api/media/assetfilters). 

> [!NOTE]
> Certifique-se de rever [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir os passos descritos neste tópico, tem de:

- Revisão [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).
- [Configurar o Postman para chamar a API de REST de serviços de multimédia do Azure](media-rest-apis-with-postman.md).

    Lembre-se de que siga o último passo no tópico [obter do Azure AD Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definir um filtro  

Segue-se a **corpo do pedido** exemplo que define as condições de seleção de controle que são adicionadas ao manifesto. Este filtro inclui qualquer faixas de áudio são EC 3 e nenhum Roteiro de vídeo com velocidade de transmissão no 0-1000000 intervalo.

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

Para obter mais informações, consulte [criar ou atualizar](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Criar filtros de elemento  

No "Serviços de multimédia v3" coleção do Postman que transferiu, selecione **ativos**->**criar ou atualizar o filtro de elemento**.

O **colocar** método de pedido HTTP é semelhante a:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Selecione o **corpo** separador e colar o json de código que [definidas anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro de elemento foi criado.

Para obter detalhes sobre como criar ou atualizar os filtros de recurso, consulte [criar ou atualizar](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros localizador de transmissão em fluxo

Pode especificar uma lista dos filtros de ativo ou a conta, que seria aplicada para o localizador de transmissão em fluxo. O [Packager dinâmico (transmissão em fluxo o ponto de extremidade)](dynamic-packaging-overview.md) aplica-se esta lista de filtros em conjunto com o seu cliente especifica no URL. Esta combinação gera uma [dinâmica manifestar](filters-dynamic-manifest-overview.md), que se baseia em filtros no URL + filtros que especificar no localizador de transmissão em fluxo. Recomendamos que utilize esta funcionalidade se pretenda aplicar filtros, mas não pretende expor os nomes de filtro no URL.

Para criar e associar filtros um localizador de transmissão em fluxo de mensagens em fila com o REST, utilize o [localizadores de transmissão em fluxo - crie](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API e especifique `properties.filters` no [corpo do pedido](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Utilizar os filtros de Stream

Depois de definir filtros, os clientes podem utilizá-los o URL de transmissão em fluxo. Filtros podem ser aplicados a velocidade de transmissão adaptável, protocolos de transmissão em fluxo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela seguinte mostra alguns exemplos de URLs com filtros:

|Protocol|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Passos Seguintes

[Vídeos do Stream](stream-files-tutorial-with-rest.md) 

---
title: Utilizar a CLI para criar filtros com os serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo mostra como usar a CLI para criar filtros com os serviços de mídia do Azure v3.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 74516aa921e45917f327a193a1c972b021c9c8ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896063"
---
# <a name="creating-filters-with-cli"></a>Criar filtros com o CLI 

Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido), o cliente poderá ter mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Serviços de multimédia do Azure permite-lhe definir os filtros de conta e filtros de recurso para o seu conteúdo. 

Para obter uma descrição detalhada desse recurso e dos cenários em que ele é usado, consulte manifestos e [filtros](filters-concept.md) [dinâmicos](filters-dynamic-manifest-overview.md) .

Este tópico mostra como configurar um filtro para um ativo do vídeo a pedido e utilizar a CLI para serviços de multimédia v3 criem [filtros de conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Asset filtros](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Certifique-se de examinar [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definir um filtro 

O exemplo seguinte define as condições de seleção de controle que são adicionadas ao manifesto final. Esse filtro inclui quaisquer faixas de áudio EC-3 e quaisquer faixas de vídeo com taxa de bits no intervalo de 0-1000000.

> [!TIP]
> Se você planeja definir **filtros** em repouso, observe que precisa incluir o objeto JSON de wrapper "Properties".  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Criar filtros de conta

O seguinte procedimento [filtro de conta do ams az](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) comando cria um filtro de conta com filtro seleções de controle que foram [definidas anteriormente](#define-a-filter). 

O comando permite passar um parâmetro de `--tracks` opcional que contém JSON representando as seleções de controle.  Use @ {file} para carregar o JSON de um arquivo. Se você estiver usando o CLI do Azure localmente, especifique o caminho do arquivo inteiro:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Criar filtros de elemento

O seguinte [filtro de elemento do ams az](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) comando cria um filtro de elemento com o filtro seleções de controle que foram [definidas anteriormente](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros ao localizador de streaming

Você pode especificar uma lista de ativos ou filtros de conta, que se aplicariam ao seu localizador de streaming. O [Gerenciador dinâmico (ponto de extremidade de streaming)](dynamic-packaging-overview.md) aplica essa lista de filtros junto com aqueles que seu cliente especifica na URL. Essa combinação gera um [manifesto dinâmico](filters-dynamic-manifest-overview.md), que é baseado em filtros na URL + filtros que você especificar no localizador de streaming. Recomendamos que você use esse recurso se desejar aplicar filtros, mas não quiser expor os nomes de filtro na URL.

O código da CLI a seguir mostra como criar um localizador de streaming e especificar `filters`. Essa é uma propriedade opcional que usa uma lista separada por espaços de nomes de filtro de ativos e/ou nomes de filtro de conta.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Fluxo usando filtros

Depois de definir filtros, os clientes podem usá-los na URL de streaming. Os filtros podem ser aplicados a protocolos de streaming de taxa de bits adaptável: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Passo seguinte

[Vídeos do Stream](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Ver também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

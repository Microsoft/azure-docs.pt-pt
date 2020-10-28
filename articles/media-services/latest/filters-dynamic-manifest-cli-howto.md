---
title: Utilize o CLI para criar filtros com a Azure Media Services Microsoft Docs
description: Este artigo mostra como usar o CLI para criar filtros com a Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f86b3fbebfaa2eb6fc35d5373eab2bb296ff5043
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740601"
---
# <a name="creating-filters-with-cli"></a>Criar filtros com CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao entregar o seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo a pedido), o seu cliente poderá necessitar de mais flexibilidade do que o descrito no ficheiro manifesto do ativo padrão. O Azure Media Services permite-lhe definir filtros de conta e filtros de ativos para o seu conteúdo. 

Para uma descrição detalhada desta característica e cenários onde é utilizado, consulte [Manifestos Dinâmicos](filters-dynamic-manifest-overview.md) e [Filtros.](filters-concept.md)

Este tópico mostra como configurar um filtro para um ativo vídeo on-demand e usar o CLI para serviços de mídia v3 para criar [filtros de conta](/cli/azure/ams/account-filter?view=azure-cli-latest) e [filtros de ativos](/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Certifique-se de rever a [apresentaçãoTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

- [Criar uma conta de Serviços de Comunicação](./create-account-howto.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definir um filtro 

O exemplo a seguir define as condições de seleção da pista que são adicionadas ao manifesto final. Este filtro inclui quaisquer faixas de áudio que sejam EC-3 e quaisquer faixas de vídeo que tenham bitrate na gama 0-100000.

> [!TIP]
> Se planeia definir **Filtros** em REST, note que precisa de incluir o objeto JSON de invólucro "Propriedades".  

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

O seguinte comando [az ams-filter](/cli/azure/ams/account-filter?view=azure-cli-latest) cria um filtro de conta com seleções de faixas de filtro que foram [definidas anteriormente](#define-a-filter). 

O comando permite-lhe passar um parâmetro opcional `--tracks` que contém JSON representando as seleções da pista.  Use @{file} para carregar JSON a partir de um ficheiro. Se estiver a utilizar o Azure CLI localmente, especifique todo o caminho do ficheiro:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Consulte também os [exemplos JSON para filtros.](/rest/api/media/accountfilters/createorupdate#create-an-account-filter)

## <a name="create-asset-filters"></a>Criar filtros de ativos

O seguinte comando [az ams-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest) cria um filtro de ativos com seleções de faixas de filtro que foram [definidas anteriormente](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Consulte também os [exemplos JSON para filtros.](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros com localizador de streaming

Pode especificar uma lista de filtros de ativos ou de conta, que se aplicaria ao seu Localizador de Streaming. O [Dynamic Packager (Streaming Endpoint)](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os que o seu cliente especifica no URL. Esta combinação gera um [Manifesto Dinâmico,](filters-dynamic-manifest-overview.md)que é baseado em filtros nos filtros URL + que especifica no Localizador de Streaming. Recomendamos que utilize esta função se pretender aplicar filtros, mas não pretende expor os nomes dos filtros no URL.

O seguinte código CLI mostra como criar um localizador de streaming e especificar `filters` . Esta é uma propriedade opcional que leva uma lista separada do espaço de nomes de filtros de ativos e/ou nomes de filtros de conta.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Fluxo usando filtros

Assim que definir filtros, os seus clientes poderão usá-los na URL de streaming. Os filtros podem ser aplicados em protocolos de streaming de bitrate adaptativo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Passo seguinte

[Stream vídeos](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Ver também

[CLI do Azure](/cli/azure/ams?view=azure-cli-latest)

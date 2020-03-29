---
title: Utilize o CLI para criar filtros com a Azure Media Services. Microsoft Docs
description: Este artigo mostra como usar o CLI para criar filtros com o Azure Media Services v3.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896063"
---
# <a name="creating-filters-with-cli"></a>Criação de filtros com CLI 

Ao entregar o seu conteúdo aos clientes (streaming de eventos ao vivo ou vídeo a pedido), o seu cliente poderá necessitar de mais flexibilidade do que o descrito no ficheiro manifesto do ativo predefinido. O Azure Media Services permite-lhe definir filtros de conta e filtros de ativos para o seu conteúdo. 

Para uma descrição detalhada desta funcionalidade e cenários onde é utilizada, consulte [Manifestos dinâmicos](filters-dynamic-manifest-overview.md) e [filtros](filters-concept.md).

Este tópico mostra como configurar um filtro para um ativo video on-demand e usar CLI para Media Services v3 para criar [filtros](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) de conta e [filtros de ativos](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Certifique-se de rever a [apresentaçãoTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

- [Criar uma conta de Media Services.](create-account-cli-how-to.md) Lembre-se do nome do grupo de recursos e do nome da conta Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Defina um filtro 

O exemplo seguinte define as condições de seleção da pista que são adicionadas ao manifesto final. Este filtro inclui quaisquer faixas áudio que sejam EC-3 e quaisquer faixas de vídeo que tenham bitrate na gama 0-10000000.

> [!TIP]
> Se planeia definir **filtros** em REST, note que precisa de incluir o objeto JSON do invólucro "Propriedades".  

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

O comando de [filtro de conta az ams](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) seguinte cria um filtro de conta com seleções de via seleções de filtro que foram [definidas anteriormente](#define-a-filter). 

O comando permite-lhe `--tracks` passar um parâmetro opcional que contém JSON representando as seleções de pista.  Utilize @{file} para carregar jSON a partir de um ficheiro. Se estiver a utilizar o Azure CLI localmente, especifique toda a trajetória de ficheiro:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Consulte também [exemplos jSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Criar filtros de ativos

O comando [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) seguinte cria um filtro de ativo com seleções de via selada de filtro que foram [definidas anteriormente](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Consulte também [exemplos jSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Filtros associados com localizador de streaming

Pode especificar uma lista de filtros de ativos ou conta, que se aplicaria ao seu Localizador de Streaming. O [Pacote Dinâmico (Streaming Endpoint)](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os especificados pelo seu cliente no URL. Esta combinação gera um [Manifesto Dinâmico,](filters-dynamic-manifest-overview.md)que é baseado em filtros no URL + filtros que especifica no Localizador de Streaming. Recomendamos que utilize esta funcionalidade se pretender aplicar filtros, mas não pretende expor os nomes dos filtros no URL.

O código CLI seguinte mostra como criar `filters`um Localizador de Streaming e especificar . Esta é uma propriedade opcional que leva uma lista separada do espaço de nomes de filtros de ativos e/ou nomes de filtro de conta.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Fluxo usando filtros

Uma vez definidofiltros, os seus clientes podem usá-los no URL de streaming. Os filtros podem ser aplicados em protocolos de streaming de bitrate adaptativo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela que se segue mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Passo seguinte

[Stream vídeos](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

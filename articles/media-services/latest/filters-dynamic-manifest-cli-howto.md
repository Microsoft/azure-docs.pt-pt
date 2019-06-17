---
title: Utilizar a CLI para criar filtros com os serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico mostra como utilizar a CLI para criar filtros com serviços de multimédia.
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
ms.openlocfilehash: c6007b66e31996db5c6b043219470968a7b05031
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67114691"
---
# <a name="creating-filters-with-cli"></a>Criar filtros com o CLI 

Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido), o cliente poderá ter mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Serviços de multimédia do Azure permite-lhe definir os filtros de conta e filtros de recurso para o seu conteúdo. 

Para obter descrição detalhada desta funcionalidade e cenários em que é utilizada, consulte [manifestos dinâmica](filters-dynamic-manifest-overview.md) e [filtros](filters-concept.md).

Este tópico mostra como configurar um filtro para um ativo do vídeo a pedido e utilizar a CLI para serviços de multimédia v3 criem [filtros de conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Asset filtros](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Certifique-se de rever [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Pré-requisitos 

- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definir um filtro 

O exemplo seguinte define as condições de seleção de controle que são adicionadas ao manifesto final. Este filtro inclui qualquer faixas de áudio são EC 3 e nenhum Roteiro de vídeo com velocidade de transmissão no 0-1000000 intervalo.

> [!TIP]
> Se pretender definir **filtros** na REST, tenha em atenção que tem de incluir o objeto JSON do wrapper de "Propriedades".  

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

O comando permite que passe opcional `--tracks` parâmetro que contém o JSON que representa as seleções de controle.  Utilize @{file} para carregar o JSON de um arquivo. Se estiver a utilizar a CLI do Azure localmente, especifique o caminho de ficheiro completo:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Criar filtros de elemento

O seguinte [filtro de elemento do ams az](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) comando cria um filtro de elemento com o filtro seleções de controle que foram [definidas anteriormente](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="associate-filters-with-streaming-locator"></a>Associar filtros localizador de transmissão em fluxo

Pode especificar uma lista dos filtros de ativo ou a conta, que seria aplicada para o localizador de transmissão em fluxo. O [Packager dinâmico (transmissão em fluxo o ponto de extremidade)](dynamic-packaging-overview.md) aplica-se esta lista de filtros em conjunto com o seu cliente especifica no URL. Esta combinação gera uma [dinâmica manifestar](filters-dynamic-manifest-overview.md), que se baseia em filtros no URL + filtros que especificar no localizador de transmissão em fluxo. Recomendamos que utilize esta funcionalidade se pretenda aplicar filtros, mas não pretende expor os nomes de filtro no URL.

O código seguinte mostra como criar um localizador de transmissão em fluxo e especifique `filters`. Essa é uma propriedade opcional que utiliza uma lista separada por espaço de nomes de filtro de elemento e/ou nomes de conta de filtro.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Utilizar os filtros de Stream

Depois de definir filtros, os clientes podem utilizá-los o URL de transmissão em fluxo. Filtros podem ser aplicados a velocidade de transmissão adaptável, protocolos de transmissão em fluxo: Apple HTTP Live Streaming (HLS), MPEG-DASH e Smooth Streaming.

A tabela seguinte mostra alguns exemplos de URLs com filtros:

|Protocol|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Transmissão em Fluxo Uniforme|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Passo seguinte

[Vídeos do Stream](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

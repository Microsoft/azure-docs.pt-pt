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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ba3de32f4ec3b9f6faf1d5a51da9c1c91e4a2e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732438"
---
# <a name="creating-filters-with-cli"></a>Criar filtros com o CLI 

Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido), o cliente poderá ter mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Serviços de multimédia do Azure permite-lhe definir os filtros de conta e filtros de recurso para o seu conteúdo. Para obter mais informações, consulte [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).

Este tópico mostra como configurar um filtro para um ativo do vídeo a pedido e utilizar a CLI para serviços de multimédia v3 criem [filtros de conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Asset filtros](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Pré-requisitos 

- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 
- Revisão [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definir um filtro 

O exemplo seguinte define as condições de seleção de controle que são adicionadas ao manifesto final. Este filtro inclui qualquer faixas de áudio são EC 3 e nenhum Roteiro de vídeo com velocidade de transmissão no 0-1000000 intervalo.

Filtros definidos no RESTANTE, incluem o objeto JSON do wrapper de "Propriedades".  

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

## <a name="next-step"></a>Passo seguinte

[Vídeos do Stream](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

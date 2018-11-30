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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 6b0ef646ba9ea535038f181ebfff5bf7639afdf8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633627"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Criar filtros com o API de REST dos serviços de multimédia

Quando a entrega de conteúdo aos clientes (transmissão em fluxo eventos em direto ou de vídeo a pedido) o cliente poderá ter mais flexibilidade do que o que é descrito no arquivo de manifesto do recurso padrão. Serviços de multimédia do Azure permite-lhe definir os filtros de conta e filtros de recurso para o seu conteúdo. Para obter mais informações, consulte [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).

Este tópico mostra como definir um filtro para ver um vídeo num recurso a pedido e utilizar REST APIs para criar [filtros de conta](https://docs.microsoft.com/rest/api/media/accountfilters) e [Asset filtros](https://docs.microsoft.com/rest/api/media/assetfilters). 

## <a name="prerequisites"></a>Pré-requisitos 

Para concluir os passos descritos neste tópico, tem de:

- Revisão [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).
- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia. 
- [Configurar o Postman para chamadas de API de REST de serviços de multimédia do Azure](media-rest-apis-with-postman.md)/

## <a name="define-a-filter"></a>Definir um filtro  

Segue-se a **corpo do pedido** exemplo que define as condições de seleção de controle que são adicionadas ao manifesto. Este filtro inclui qualquer faixas de áudio que estão em inglês, com EC 3 e nenhum Roteiro de vídeo com velocidade de transmissão no 0-1000000 intervalo.

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
                        "property": "Language",
                        "value": "en",
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
     }
}
```

## <a name="create-account-filters"></a>Criar filtros de conta

Coleção do Postman que transferiu, selecione **filtros de conta**->**criar ou atualizar um filtro de conta**.

O **colocar** método de pedido HTTP é semelhante a:

PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01

Selecione o **corpo** separador e colar o json de código que [definidas anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro foi criado.

Para obter mais informações, consulte [criar ou atualizar](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Criar filtros de elemento  

No "Serviços de multimédia v3" coleção do Postman que transferiu, selecione **ativos**-> * * criar ou atualizar o filtro de elemento.

O **colocar** método de pedido HTTP é semelhante a:

PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01

Selecione o **corpo** separador e colar o json de código que [definidas anteriormente](#define-a-filter).

Selecione **Enviar**. 

O filtro de elemento foi criado.

Para obter detalhes sobre como criar ou atualizar os filtros de recurso, consulte [criar ou atualizar](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Além disso, veja [exemplos JSON para filtros](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Passos Seguintes

[Vídeos do Stream](stream-files-tutorial-with-rest.md) 

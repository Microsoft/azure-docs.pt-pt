---
title: Nuvens e regiões nas quais os serviços de mídia do Azure v3 estão disponíveis
description: Este artigo fala sobre as nuvens e regiões do Azure nas quais os serviços de mídia do Azure v3 estão disponíveis.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 58b5b749e81aab4d8563d09cbfd139629520531c
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310578"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Nuvens e regiões nas quais os serviços de mídia do Azure v3 existem

Os serviços de mídia do Azure v3 estão disponíveis por meio do manifesto Azure Resource Manager no Azure global, Azure governamental, Azure Alemanha, Azure China 21Vianet. No entanto, nem todos os recursos dos serviços de mídia estão disponíveis em todas as nuvens do Azure. Este documento descreve o Availabilities dos principais componentes dos serviços de mídia v3.

## <a name="feature-availability-in-azure-clouds"></a>Disponibilidade de recursos em nuvens do Azure

| Funcionalidade|Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [EventGrid do Azure](reacting-to-media-services-events.md) | Disponível | Não disponível | Não disponível | Não disponível |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [StandardEncoderPreset](encoding-concept.md) | Disponível | Disponível | Disponível | Disponível |
| [LiveEvents](live-streaming-overview.md) | Disponível | Disponível | Disponível | Disponível |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Disponível | Disponível | Disponível | Disponível |

## <a name="regionsgeographieslocations"></a>Regiões/geografias/locais

[Regiões nas quais o serviço dos serviços de mídia do Azure está implantado](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Nome do código de região 

Quando precisar fornecer o parâmetro **Location** , você precisa fornecer o nome do código de região como o valor de **local** . Para obter o nome do código da região em que sua conta está e para a qual sua chamada deve ser roteada, você pode executar a seguinte linha no [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```bash
az account list-locations
```

Depois de executar a linha mostrada acima, você obterá uma lista de todas as regiões do Azure. Navegue até a região do Azure que tem o *DisplayName* que você está procurando e use seu valor de *nome* para o parâmetro **Location** .

Por exemplo, para a região do Azure oeste dos EUA 2 (exibido abaixo), você usará "westus2" para o parâmetro **Location** .

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Pontos Finais  

Os pontos de extremidade a seguir são importantes para saber ao se conectar a contas de serviços de mídia de diferentes nuvens nacionais do Azure.

### <a name="global-azure"></a>Azure global

|Pontos Finais ||
| --- | --- | 
| Gestor de Recursos do Azure |  `https://management.azure.com/` |
| Autenticação | `https://login.microsoftonline.com/` | 
| Audiência do token | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Pontos Finais||
| --- | --- | 
| Gestor de Recursos do Azure |  `https://management.usgovcloudapi.net/` |
| Autenticação | `https://login.microsoftonline.us/` | 
| Audiência do token | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Alemanha

| Pontos Finais ||
| --- | --- |  
| Gestor de Recursos do Azure | `https://management.cloudapi.de/` |
| Autenticação | `https://login.microsoftonline.de/` |
| Audiência do token | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Pontos Finais||
| --- | --- | 
| Gestor de Recursos do Azure | `https://management.chinacloudapi.cn/` |
| Autenticação | `https://login.chinacloudapi.cn/` |
| Audiência do token |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Ver também

* [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos serviços de mídia v3](media-services-overview.md)

---
title: Nuvens e regiões em que o Azure Media Services v3 está disponível
description: Este artigo fala sobre nuvens e regiões azure nas quais o Azure Media Services v3 está disponível.
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
ms.openlocfilehash: 1257bf4dfb0d5b2c4995cac760290f97293a0c0f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382975"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Nuvens e regiões em que existe azure Media Services v3

O Azure Media Services v3 está disponível através do manifesto Azure Resource Manager no global Azure, Azure Government, Azure Germany, Azure China 21Vianet. No entanto, nem todas as funcionalidades dos Serviços de Media estão disponíveis em todas as nuvens do Azure. Este documento descreve as disponibilidades dos principais componentes v3 dos Serviços de Media.

## <a name="feature-availability-in-azure-clouds"></a>Disponibilidade de funcionalidades em nuvens Azure

| Funcionalidade|Regiões Azure Globais | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Disponível | Não disponível | Não disponível | Não disponível |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [StandardEncoderPreset](encoding-concept.md) | Disponível | Disponível | Disponível | Disponível |
| [Eventos ao Vivo](live-streaming-overview.md) | Disponível | Disponível | Disponível | Disponível |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Disponível | Disponível | Disponível | Disponível |

## <a name="regionsgeographieslocations"></a>Regiões/geografias/localizações

[Regiões em que o serviço Azure Media Services é implantado](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Nome de código da região

Quando precisa fornecer o parâmetro de **localização,** precisa fornecer o nome de código da região como o valor de **localização.** Para obter o nome de código da região em que a sua conta está e que a sua chamada deve ser encaminhada para, você pode executar a seguinte linha em [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Uma vez que você executa a linha mostrada acima, você obtém uma lista de todas as regiões Azure. Navegue para a região de Azure que tenha o nome de *exibição* que procura e use o seu valor de *nome* para o parâmetro de **localização.**

Por exemplo, para a região de Azure West US 2 (exibida abaixo), você usará "westus2" para o parâmetro de **localização.**

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

Os seguintes pontos finais são importantes de saber quando se conectam às contas dos Serviços de Media de diferentes nuvens nacionais do Azure.

### <a name="global-azure"></a>Global Azure

|Pontos Finais||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Autenticação | `https://login.microsoftonline.com/` |
| Audiência simbólica | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Pontos Finais||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Autenticação | `https://login.microsoftonline.us/` |
| Audiência simbólica | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Alemanha

| Pontos Finais ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Autenticação | `https://login.microsoftonline.de/` |
| Audiência simbólica | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Pontos Finais||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Autenticação | `https://login.chinacloudapi.cn/` |
| Audiência simbólica |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Consulte também

* [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Localizações azure](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Passos seguintes

[Visão geral do Media Services v3](media-services-overview.md)

---
title: Nuvens e regiões em que o Azure Media Services v3 está disponível
description: Este artigo fala sobre nuvens e regiões do Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 9c54482dc188141c4fc697bd43628b81e0ddc29f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597131"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Nuvens e regiões em que existem serviços de mídia azul v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services v3 está disponível através do manifesto Azure Resource Manager em Global Azure, Azure Government, Azure Germany, Azure China 21Vianet. No entanto, nem todas as funcionalidades dos Media Services estão disponíveis em todas as nuvens do Azure. Este documento descreve as disponibilidades dos principais componentes dos Media Services v3.

## <a name="feature-availability-in-azure-clouds"></a>Disponibilidade de recursos em nuvens Azure

| Funcionalidade|Regiões Azure Globais | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Disponível | Não disponível | Não disponível | Não disponível |
| [VídeoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [StandardEncoderPreset](encoding-concept.md) | Disponível | Disponível | Disponível | Disponível |
| [LiveEvents](live-streaming-overview.md) | Disponível | Disponível | Disponível | Disponível |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Disponível | Disponível | Disponível | Disponível |

## <a name="feature-availability-in-preview"></a>Disponibilidade de funcionalidades na pré-visualização

[LiveTranscription](live-transcription.md) está disponível nas seguintes regiões:

- Sudeste Asiático
- Europa Ocidental
- Europa do Norte
- E.U.A. Leste
- E.U.A. Central
- E.U.A. Centro-Sul
- E.U.A. Oeste 2
- Sul do Brasil

## <a name="regionsgeographieslocations"></a>Regiões/geografias/localizações

[Regiões em que o serviço Azure Media Services é implantado](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Nome do código da região

Quando precisa fornecer o parâmetro de **localização,** precisa fornecer o nome de código da região como o valor de **localização.** Para obter o nome de código da região em que a sua conta está e que a sua chamada deve ser encaminhada para, pode executar a seguinte linha em [Azure CLI](/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Uma vez que você executar a linha mostrada acima, você obtém uma lista de todas as regiões Azure. Navegue para a região de Azure que tem o *nome de exibição* E use o seu valor *de nome* para o parâmetro **de localização.**

Por exemplo, para a região de Azure West US 2 (apresentado abaixo), você usará "westus2" para o parâmetro de **localização.**

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

Os seguintes pontos finais são importantes para saber ao ligar-se às contas dos Serviços de Media de diferentes nuvens nacionais do Azure.

### <a name="global-azure"></a>Global Azure

| Serviço | Ponto final |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Autenticação | `https://login.microsoftonline.com/` |
| Público simbólico | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Serviço | Ponto final |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Autenticação | `https://login.microsoftonline.us/` |
| Público simbólico | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Alemanha

| Serviço | Ponto final |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Autenticação | `https://login.microsoftonline.de/` |
| Público simbólico | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Serviço | Ponto final |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Autenticação | `https://login.chinacloudapi.cn/` |
| Público simbólico |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Consulte também

* [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Locais azuis](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos Serviços de Comunicação Social v3](media-services-overview.md)

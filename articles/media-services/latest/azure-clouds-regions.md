---
title: Regiões nos quais serviços de multimédia do Azure v3 está disponível e nuvens | Documentos da Microsoft
description: Este artigo fala sobre clouds do Azure e regiões nos quais serviços de multimédia do Azure v3 está disponível.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/07/2019
ms.author: juliako
ms.openlocfilehash: cadf4c564ab8600094bc545ae7270eb9c0642e1e
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890965"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Nuvens e regiões nos quais serviços de multimédia do Azure v3 existe

Serviços de multimédia do Azure v3 está disponível através do Azure Resource Manager manifesto no global Azure, Azure Government, Azure Alemanha, Azure China 21Vianet. No entanto, nem todas as funcionalidades de serviços de multimédia estão disponíveis em todas as nuvens do Azure. Este documento descreve disponibilidades dos principais componentes do serviços de multimédia v3.

## <a name="feature-availability-in-azure-clouds"></a>Disponibilidade de funcionalidades em clouds do Azure

| Funcionalidade|Regiões globais do Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Disponível | Não disponível | Não disponível | Não disponível |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Disponível | Não disponível | Não disponível | Não disponível |
| [StandardEncoderPreset](encoding-concept.md) | Disponível | Disponível | Disponível | Disponível |
| [LiveEvents](live-streaming-overview.md) | Disponível | Disponível | Disponível | Disponível |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Disponível | Disponível | Disponível | Disponível |

## <a name="regionsgeographieslocations"></a>As geografias/regiões/localizações

* [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Produtos por região](https://azure.microsoft.com/global-infrastructure/services/)
* [Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Localizações do Azure](https://azure.microsoft.com/global-infrastructure/locations/)

### <a name="region-code-name"></a>Nome do código de região 

Quando precisa fornecer o **localização** parâmetro, tem de fornecer o nome de código de região que o **localização** valor. Para obter o nome de código da região que pertença a sua conta e que sua chamada deve ser roteada para, pode executar a seguinte linha [da CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```bash
az account list-locations
```

Uma vez que executar a linha mostrada acima, obtém uma lista de todas as regiões do Azure. Navegue para a região do Azure com o *displayName* está procurando e utilizar seus *nome* valor para o **localização** parâmetro.

Por exemplo, para a região do Azure E.U.A. oeste 2 (apresentado abaixo), irá utilizar o "westus2" para o **localização** parâmetro.

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

| Ponto Final|Global Azure | Azure Government|Azure Alemanha|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` | `https://management.usgovcloudapi.net/` | `https://management.cloudapi.de/` | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.microsoftonline.com/` | `https://login.microsoftonline.us/` | `https://login.microsoftonline.de/` | `https://login.chinacloudapi.cn/` |
| Audiência do token | `https://management.core.windows.net/` | `https://management.core.usgovcloudapi.net/` | `https://management.core.cloudapi.de/`| `https://management.core.chinacloudapi.cn/` |

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral do serviços de multimédia v3](media-services-overview.md)

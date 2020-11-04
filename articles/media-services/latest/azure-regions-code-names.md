---
title: Nuvens e regiões em que o Azure Media Services v3 está disponível
description: Este artigo fala sobre os URLs utilizados para os pontos finais e código para as regiões.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: c17eeda91af46c4159f7c4b1747416298f24c703
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319642"
---
# <a name="regional-code-names-and-endpoints"></a>Nomes de código regionais e pontos finais

### <a name="region-code-name"></a>Nome do código da região

Quando o parâmetro de **localização** é utilizado num comando ou pedido, é necessário fornecer o nome de código da região como valor de **localização.** Para obter o nome de código da região em que a sua conta está e que a sua chamada deve ser encaminhada para, pode executar a seguinte linha em Azure CLI.

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

> [!NOTE]
> Os pontos finais da Alemanha azul aplicam-se apenas às nuvens soberanas na Alemanha.

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

## <a name="see-also"></a>Ver também

* [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)
* [Nomes de código regionais e pontos finais](azure-regions-code-names.md)
* [Geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Locais azuis](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos Serviços de Comunicação Social v3](media-services-overview.md)

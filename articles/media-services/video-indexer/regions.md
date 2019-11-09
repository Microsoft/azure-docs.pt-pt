---
title: Regiões nas quais Video Indexer está disponível-Azure
titleSuffix: Azure Media Services
description: Este artigo fala sobre regiões do Azure nas quais Video Indexer está disponível.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b67c89e02091d57b4f2dc901005c2dae36deafc2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838189"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiões do Azure nas quais Video Indexer existe

As APIs de Video Indexer contêm um parâmetro de **local** que você deve definir para a região do Azure para a qual a chamada deve ser roteada. Essa deve ser uma [região do Azure na qual Video indexer está disponível](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Localizações

O parâmetro **Location** deve receber o nome do código de região do Azure como seu valor. Se você estiver usando Video Indexer no modo de visualização, deverá colocar *"avaliação"* como o valor. Caso contrário, para obter o nome do código da região do Azure em que sua conta está e para a qual sua chamada deve ser roteada, você pode executar a seguinte linha no [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
az account list-locations
```

Depois de executar a linha mostrada acima, você obterá uma lista de todas as regiões do Azure. Navegue até a região do Azure que tem o *DisplayName* que você está procurando e use seu valor de *nome* para o parâmetro **Location** .

Por exemplo, para a região do Azure oeste dos EUA 2 (exibido abaixo), você usará "westus2" para o parâmetro **Location** .

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Passos seguintes

- [Personalizar o modelo de linguagem usando APIs](customize-language-model-with-api.md)
- [Personalizar o modelo de marcas usando APIs](customize-brands-model-with-api.md)
- [Personalizar o modelo Person usando APIs](customize-person-model-with-api.md)

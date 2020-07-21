---
title: Regiões em que o Indexante de Vídeo está disponível - Azure
titleSuffix: Azure Media Services
description: Este artigo fala sobre as regiões do Azure em que o Azure Media Services Video Indexer está disponível.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 45da0556d074fdb81002f73cd89d0c35cb37276e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530054"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiões do azul em que existe indexante de vídeo

As APIs do Indexante de Vídeo contêm um parâmetro **de localização** que deve definir para a região de Azure para onde a chamada deve ser encaminhada. Esta deve ser uma [região de Azure em que o Video Indexer está disponível](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Localizações

O `location` parâmetro deve ser dado como valor o nome de código da região de Azure. Se estiver a utilizar o Video Indexer no modo de pré-visualização, deverá colocar `"trial"` como valor. `trial`é o valor padrão para o `location` parâmetro. Caso contrário, para obter o nome de código da região Azure em que a sua conta está e que a sua chamada deve ser encaminhada para, pode executar a seguinte linha em [Azure CLI](/cli/azure):

```azurecli-interactive
az account list-locations
```

Uma vez que você executar a linha mostrada acima, você obtém uma lista de todas as regiões Azure. Navegue para a região de Azure que tem o *nome de exibição* E use o seu valor *de nome* para o parâmetro **de localização.**

Por exemplo, para a região de Azure West US 2 (apresentado abaixo), você usará "westus2" para o parâmetro de **localização.**

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

## <a name="next-steps"></a>Próximos passos

- [Personalizar modelo de linguagem usando APIs](customize-language-model-with-api.md)
- [Personalize o modelo de Marcas usando APIs](customize-brands-model-with-api.md)
- [Personalizar modelo pessoa usando APIs](customize-person-model-with-api.md)

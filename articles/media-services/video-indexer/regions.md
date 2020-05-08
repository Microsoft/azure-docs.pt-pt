---
title: Regiões em que o Indexer de Vídeo está disponível - Azure
titleSuffix: Azure Media Services
description: Este artigo fala sobre as regiões azure nas quais o Azure Media Services Video Indexer está disponível.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b71890323f6aca92fb9f847252feed57d8db234f
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594955"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiões azure em que existe Indexer de Vídeo

As APIs do Indexer de Vídeo contêm um parâmetro de **localização** que deve definir para a região de Azure para onde a chamada deve ser encaminhada. Esta deve ser uma [região azure na qual o Video Indexer está disponível.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

## <a name="locations"></a>Localizações

O `location` parâmetro deve ser dado como valor ao nome de código da região de Azure. Se estiver a utilizar o Indexer de `"trial"` Vídeo no modo de pré-visualização, deverá colocar como valor. `trial`é o valor `location` padrão para o parâmetro. Caso contrário, para obter o nome de código da região azure em que a sua conta está e que a sua chamada deve ser encaminhada para, pode executar a seguinte linha em [Azure CLI:](/cli/azure)

```azurecli-interactive
az account list-locations
```

Uma vez que você executa a linha mostrada acima, você obtém uma lista de todas as regiões Azure. Navegue para a região de Azure que tenha o nome de *exibição* que procura e use o seu valor de *nome* para o parâmetro de **localização.**

Por exemplo, para a região de Azure West US 2 (exibida abaixo), você usará "westus2" para o parâmetro de **localização.**

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

- [Personalizar modelo de idioma usando APIs](customize-language-model-with-api.md)
- [Personalizar modelo de marcas usando APIs](customize-brands-model-with-api.md)
- [Personalizar modelo pessoa usando APIs](customize-person-model-with-api.md)

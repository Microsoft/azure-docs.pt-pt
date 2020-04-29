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
ms.openlocfilehash: c91b38fcbfb9b517651adead010408425e519a82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80382754"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiões azure em que existe Indexer de Vídeo

As APIs do Indexer de Vídeo contêm um parâmetro de **localização** que deve definir para a região de Azure para onde a chamada deve ser encaminhada. Esta deve ser uma [região azure na qual o Video Indexer está disponível.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

## <a name="locations"></a>Localizações

O parâmetro de **localização** deve ser dado como valor ao nome de código da região de Azure. Se estiver a utilizar o Indexer de Vídeo no modo de pré-visualização, deve colocar *"trial"* como o valor. Caso contrário, para obter o nome de código da região azure em que a sua conta está e que a sua chamada deve ser encaminhada para, pode executar a seguinte linha em [Azure CLI:](/cli/azure)

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

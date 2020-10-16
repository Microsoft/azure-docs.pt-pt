---
title: Regiões em que o Indexante de Vídeo está disponível - Azure
titleSuffix: Azure Media Services
description: Este artigo fala sobre as regiões do Azure em que o Azure Media Services Video Indexer está disponível.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530947"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiões do azul em que existe indexante de vídeo

As APIs do Indexante de Vídeo contêm um parâmetro **de localização** que deve definir para a região de Azure para onde a chamada deve ser encaminhada. Esta deve ser uma [região de Azure em que o Video Indexer está disponível](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Localizações

O `location` parâmetro deve ser dado como valor o nome de código da região de Azure. Se estiver a utilizar o Video Indexer no modo de pré-visualização, deverá colocar `"trial"` como valor. `trial` é o valor padrão para o `location` parâmetro. Caso contrário, para obter o nome de código da região Azure em que a sua conta está e que a sua chamada deve ser encaminhada para, pode utilizar o portal Azure ou executar um comando [Azure CLI.](/cli/azure)

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no site do [Video Indexer](https://www.videoindexer.ai/).
1. Selecione **as contas do Utilizador** no canto superior direito da página.
1. Encontre a localização da sua conta no canto superior direito.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Localização":::
    
###  <a name="cli-command"></a>Comando CLI

```azurecli-interactive
az account list-locations
```

Uma vez que você executar a linha mostrada acima, você obtém uma lista de todas as regiões Azure. Navegue para a região de Azure que tem o *nome de exibição* E use o seu valor *de nome* para o parâmetro **de localização.**

Por exemplo, para a região de Azure West US 2 (apresentado abaixo), você usará "westus2" para o parâmetro de **localização.**

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Passos seguintes

- [Personalizar modelo de linguagem usando APIs](customize-language-model-with-api.md)
- [Personalize o modelo de Marcas usando APIs](customize-brands-model-with-api.md)
- [Personalizar modelo pessoa usando APIs](customize-person-model-with-api.md)

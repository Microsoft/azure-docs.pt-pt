---
title: Regiões em que está disponível - indexador de vídeo do Azure
titlesuffix: Azure Media Services
description: Este artigo fala sobre as regiões do Azure em que o indexador de vídeo está disponível.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 404aaf91c0cb30df0a83353ef7397987ec3f8e80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799419"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Regiões do Azure na qual existe Video Indexer

APIs de indexador de vídeo contém um **localização** parâmetro que deve ser definido para a região do Azure para o qual deve ser roteada, a chamada. Tem de ser um [região do Azure em que o indexador de vídeo está disponível](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Localizações

O **localização** parâmetro tem de obter o nome de código de região do Azure como seu valor. Se estiver a utilizar o Video Indexer no modo de pré-visualização, deve colocar *"avaliação"* como o valor. Caso contrário, para obter o nome de código da região do Azure que pertença a sua conta e que sua chamada deve ser roteada para, pode executar a seguinte linha [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
az account list-locations
```

Uma vez que executar a linha mostrada acima, obtém uma lista de todas as regiões do Azure. Navegue para a região do Azure com o *displayName* está procurando e utilizar seus *nome* valor para o **localização** parâmetro.

Por exemplo, para a região do Azure E.U.A. oeste 2 (apresentado abaixo), irá utilizar o "westus2" para o **localização** parâmetro.

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

## <a name="next-steps"></a>Passos Seguintes

- [Personalizar o modelo de idioma com APIs](customize-language-model-with-api.md)
- [Personalizar o modelo de marcas com APIs](customize-brands-model-with-api.md)
- [Personalizar o modelo de pessoa com APIs](customize-person-model-with-api.md)

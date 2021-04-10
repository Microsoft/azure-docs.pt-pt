---
title: Políticas de streaming nos serviços de mídia Azure
description: Este artigo dá uma explicação sobre o que são as Políticas de Streaming e como são usadas pela Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: inhenkel
ms.openlocfilehash: 2873486b95f0bb4a9a27e2e050cc6f6d5473a44e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898177"
---
# <a name="streaming-policies"></a>Políticas de Transmissão em Fluxo

No Azure Media Services v3, [as Políticas de Streaming](/rest/api/media/streamingpolicies) permitem-lhe definir protocolos de streaming e opções de encriptação para os seus [Localizadores de Streaming.](streaming-locators-concept.md) O Media Services v3 fornece algumas Políticas de Streaming predefinidas para que possa usá-las diretamente para teste ou produção. 

As políticas de streaming pré-indefinidas atualmente disponíveis:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

A seguinte "Árvore de Decisão" ajuda-o a escolher uma Política de Streaming predefinida para o seu cenário.

> [!IMPORTANT]
> * As propriedades das Políticas de **Streaming** que são do tipo Datatime estão sempre em formato UTC.
> * Deve conceber um conjunto limitado de políticas para a sua conta de Media Service e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções. Para mais informações, consulte [Quotas e limites.](limits-quotas-constraints.md)

## <a name="decision-tree"></a>Árvore de decisões

Clique na imagem para visualizá-lo tamanho completo.  

[![Diagrama mostrando uma árvore de decisão que é projetada para ajudá-lo a escolher uma política de streaming predefinida para o seu cenário.](./media/streaming-policy/large.png)](./media/streaming-policy/large.png#lightbox)

Se encriptar o seu conteúdo, tem de criar uma [Política de Chave de Conteúdo,](content-key-policy-concept.md)a Política de Chave de **Conteúdo** não é necessária para um streaming ou descarregamento claros. 

Se tiver requisitos especiais (por exemplo, se quiser especificar diferentes protocolos, precisa de utilizar um serviço de entrega de chaves personalizado ou precisar de utilizar uma faixa de áudio clara), pode [criar](/rest/api/media/streamingpolicies/create) uma Política de Streaming personalizada. 

## <a name="get-a-streaming-policy-definition"></a>Obtenha uma definição de Política de Streaming  

Se quiser ver a definição de uma Política de Streaming, use [Get](/rest/api/media/streamingpolicies/get) e especifique o nome da política. Por exemplo:

### <a name="rest"></a>REST

Pedido:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Resposta:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtragem, encomenda, paging

Ver [Filtragem, encomenda, paging de entidades de Serviços de Comunicação](entities-overview.md)Social.

## <a name="next-steps"></a>Passos seguintes

* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Utilize encriptação dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)

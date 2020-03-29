---
title: Políticas de Streaming nos Serviços de Media Azure [ Microsoft Docs
description: Este artigo dá uma explicação sobre o que são as Políticas de Streaming e como são usadas pela Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66392914"
---
# <a name="streaming-policies"></a>Políticas de Transmissão em Fluxo

No Azure Media Services v3, [as Políticas de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) permitem-lhe definir protocolos de streaming e opções de encriptação para os seus [Localizadores de Streaming](streaming-locators-concept.md). O Media Services v3 fornece algumas Políticas de Streaming predefinidas para que possa usá-las diretamente para ensaio ou produção. 

As políticas de streaming predefinidas atualmente disponíveis:<br/>
* 'Predefined_DownloadOnly'
* "Predefined_ClearStreamingOnly"
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* "Predefined_MultiDrmCencStreaming" 
* "Predefined_MultiDrmStreaming"

A seguinte "Árvore de Decisão" ajuda-o a escolher uma Política de Streaming predefinida para o seu cenário.

> [!IMPORTANT]
> * Propriedades de Políticas de **Streaming** que são do tipo Datatime estão sempre em formato UTC.
> * Deverá conceber um conjunto limitado de políticas para a sua conta de Serviço de Media e reutilizá-las para os seus Localizadores de Streaming sempre que forem necessárias as mesmas opções. Para mais informações, consulte [Quotas e limitações.](limits-quotas-constraints.md)

## <a name="decision-tree"></a>Árvore de decisões

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Se encriptar o seu conteúdo, precisa de criar uma [Política de Chave](content-key-policy-concept.md)de Conteúdo , a Política chave de **conteúdo** não é necessária para um streaming ou download claros. 

Se tiver requisitos especiais (por exemplo, se quiser especificar diferentes protocolos, precisa de utilizar um serviço de entrega de chaves personalizadas ou precisar de utilizar uma faixa de áudio clara), pode [criar](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) uma Política de Streaming personalizada. 

## <a name="get-a-streaming-policy-definition"></a>Obtenha uma definição de Política de Streaming  

Se quiser ver a definição de uma Política de Streaming, use [Get](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) e especifique o nome da política. Por exemplo:

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

Ver [Filtragem, encomenda, paging de entidades dos Serviços de Media.](entities-overview.md)

## <a name="next-steps"></a>Passos seguintes

* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Utilize encriptação dinâmica DRM e serviço de entrega de licenças](protect-with-drm.md)

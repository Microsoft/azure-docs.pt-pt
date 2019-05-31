---
title: Transmissão em fluxo políticas nos serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo fornece uma explicação sobre o que são políticas de transmissão em fluxo e como elas são usadas pelos serviços de multimédia do Azure.
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
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392914"
---
# <a name="streaming-policies"></a>Políticas de Transmissão em Fluxo

Em serviços de multimédia do Azure v3, [políticas de transmissão em fluxo](https://docs.microsoft.com/rest/api/media/streamingpolicies) permitem-lhe definir os protocolos de transmissão em fluxo e opções de encriptação para sua [localizadores de transmissão em fluxo](streaming-locators-concept.md). Serviços de multimédia v3 fornece que alguns predefinidas políticas de transmissão em fluxo para que pode usá-los diretamente para a versão de avaliação ou de produção. 

Atualmente disponível predefinidas políticas de transmissão em fluxo:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

A seguinte "árvore de decisões" ajuda a escolher uma política predefinida de transmissão em fluxo para o seu cenário.

> [!IMPORTANT]
> * Propriedades de **políticas de transmissão em fluxo** que são de Datetime tipo são sempre em formato UTC.
> * Deve criar um conjunto limitado de políticas para a sua conta de serviço de multimédia e reutilizá-los para os localizadores de transmissão em fluxo sempre que as mesmas opções são necessárias. Para obter mais informações, consulte [Quotas e limitações](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Árvore de decisões

Clique na imagem para visualizá-lo tamanho completo.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Se a encriptar o seu conteúdo, terá de criar uma [política de chave de conteúdo](content-key-policy-concept.md), o **política de chave de conteúdo** não é necessária para limpar streaming ou baixar. 

Se tiver requisitos especiais (por exemplo, se pretende especificar diferentes protocolos, tem de utilizar um serviço de entrega de chave personalizado ou tem de utilizar um Roteiro claro áudio), pode [criar](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) uma política personalizada de transmissão em fluxo. 

## <a name="get-a-streaming-policy-definition"></a>Obter uma definição de política de transmissão em fluxo  

Se quiser ver a definição de uma política de transmissão em fluxo, utilize [obter](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) e especifique o nome da política. Por exemplo:

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

## <a name="filtering-ordering-paging"></a>Paginação de filtragem, ordenação,

Ver [filtragem, ordenação, a paginação de entidades de serviços de multimédia](entities-overview.md).

## <a name="next-steps"></a>Passos Seguintes

* [Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
* [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Utilizar DRM dinâmico licença e de encriptação de serviço de entrega](protect-with-drm.md)

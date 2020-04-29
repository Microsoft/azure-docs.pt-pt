---
title: Transcrição em direto
titleSuffix: Azure Media Services
description: Saiba mais sobre a transcrição ao vivo da Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499869"
---
# <a name="live-transcription-preview"></a>Transcrição ao vivo (pré-visualização)

O Azure Media Service entrega vídeo, áudio e texto em diferentes protocolos. Quando publica o seu live stream utilizando o MPEG-DASH ou hlS/CMAF, em seguida, juntamente com vídeo e áudio, o nosso serviço fornece o texto transcrito em IMSC1.1 compatível Com TTML. A entrega é embalada em fragmentos mPEG-4 Parte 30 (ISO/IEC 14496-30). Se utilizar a entrega via HLS/TS, o texto é entregue como VTT em pedaços.

Este artigo descreve como permitir a transcrição ao vivo ao transmitir um Evento Ao Vivo com a Azure Media Services v3. Antes de continuar, certifique-se de que está familiarizado com a utilização de Media Services v3 REST APIs (consulte [este tutorial](stream-files-tutorial-with-rest.md) para mais detalhes). Você também deve estar familiarizado com o conceito [de streaming ao vivo.](live-streaming-overview.md) É aconselhável completar o Stream em direto com o tutorial [dos Media Services.](stream-live-tutorial-with-api.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como uma funcionalidade de pré-visualização na região oeste dos EUA 2. Apoia a transcrição de palavras faladas em inglês para texto. A referência da API para esta funcionalidade está localizada abaixo — becasuse está em pré-visualização, os detalhes não estão disponíveis com os nossos documentos REST.

## <a name="creating-the-live-event"></a>Criação do Evento Ao Vivo

Para criar o Live Event, envia a operação PUT para a versão de pré-visualização 2019-05-01, por exemplo:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A operação tem o seguinte corpo (onde é criado um Evento Live pass-through com a RTMP como protocolo de ingerir). Note a adição de uma propriedade de transcrições. O único valor permitido para a linguagem é en-US.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Faça uma sondagem sobre o estado do Evento Ao Vivo até entrar no estado "Running", o que indica que agora pode enviar uma contribuição para o feed RTMP. Agora pode seguir os mesmos passos que neste tutorial, como verificar o feed de pré-visualização e criar saídas ao vivo.

## <a name="transcription-delivery-and-playback"></a>Entrega e reprodução de transcrição

Reveja o artigo de visão geral da [embalagem Dinâmica](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) de como o nosso serviço utiliza embalagens dinâmicas para entregar vídeo, áudio e texto em diferentes protocolos. Quando publica o seu live stream utilizando o MPEG-DASH ou hlS/CMAF, em seguida, juntamente com vídeo e áudio, o nosso serviço fornece o texto transcrito em IMSC1.1 compatível Com TTML. Esta entrega é embalada em fragmentos mPEG-4 Parte 30 (ISO/IEC 14496-30). Se utilizar a entrega via HLS/TS, o texto é entregue como VTT em pedaços. Pode utilizar um web player como o [Azure Media Player](use-azure-media-player.md) para reproduzir o stream.  

> [!NOTE]
> Se utilizar o Azure Media Player, utilize a versão 2.3.3 ou posterior.

## <a name="known-issues"></a>Problemas conhecidos

Para pré-visualização, são conhecidas questões conhecidas com transcrição ao vivo:

* A funcionalidade está disponível apenas no West US 2.
* As aplicações precisam de utilizar as APIs de pré-visualização, descritas na [Especificação OpenAPI](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)dos Media Services v3 .
* A única língua apoiada é o inglês (en-us).
* Com proteção de conteúdo, apenas a encriptação do envelope AES é suportada.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral dos Serviços de Media](media-services-overview.md)

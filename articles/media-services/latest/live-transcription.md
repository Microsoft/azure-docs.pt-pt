---
title: Transcrição em direto
titleSuffix: Azure Media Services
description: Saiba mais sobre a transcrição em direto da Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: inhenkel
ms.openlocfilehash: 9481b4ee2f225c7f76337d73b27630e4c67cc780
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193613"
---
# <a name="live-transcription-preview"></a>Transcrição ao vivo (pré-visualização)

O Azure Media Service fornece vídeo, áudio e texto em diferentes protocolos. Quando publica o seu live stream utilizando MPEG-DASH ou HLS/CMAF, em seguida, juntamente com vídeo e áudio, o nosso serviço entrega o texto transcrito em TTML compatível com IMSC1.1. A entrega é embalada em fragmentos MPEG-4 Parte 30 (ISO/IEC 14496-30). Se utilizar a entrega via HLS/TS, o texto é entregue como VTT em pedaços.

Este artigo descreve como permitir a transcrição ao vivo ao transmitir um Evento Ao Vivo com a Azure Media Services v3. Antes de continuar, certifique-se de que está familiarizado com a utilização de Media Services v3 REST APIs (consulte [este tutorial](stream-files-tutorial-with-rest.md) para mais detalhes). Você também deve estar familiarizado com o conceito [de streaming ao vivo.](live-streaming-overview.md) Recomenda-se completar o Stream ao vivo com o tutorial [dos Media Services.](stream-live-tutorial-with-api.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como uma funcionalidade de pré-visualização na região oeste dos EUA 2. Apoia a transcrição de palavras faladas em inglês para texto. A referência da API para esta funcionalidade está localizada abaixo — becasuse que está em pré-visualização, os detalhes não estão disponíveis com os nossos documentos REST.

## <a name="creating-the-live-event"></a>Criação do Evento Ao Vivo

Para criar o Live Event, envia a operação PUT para a versão de pré-visualização 2019-05-01, por exemplo:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A operação tem o seguinte corpo (onde é criado um Evento Ao Vivo com RTMP como protocolo de ingestão). Note a adição de uma propriedade de transcrições. O único valor permitido para a linguagem é en-US.

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

Faça a sondagem do estado do Live Event até entrar no estado "Running", o que indica que agora pode enviar uma contribuição de feed RTMP. Agora pode seguir os mesmos passos que neste tutorial, como verificar o feed de pré-visualização e criar saídas ao vivo.

## <a name="start-transcription-after-live-event-has-started"></a>Comece a transcrição após evento ao vivo já começou

A transcrição ao vivo pode ser iniciada depois de um evento ao vivo ter começado. Para ligar transcrições ao vivo, remendo o evento ao vivo para incluir a propriedade "transcrições". Para desligar as transcrições ao vivo, a propriedade "transcrições" será removida do objeto do evento ao vivo.

> [!NOTE]
> Ligar ou desligar a transcrição mais de uma vez durante o evento ao vivo não é um cenário apoiado.

Esta é a chamada para ligar transcrições ao vivo.

PATCH:```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

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

## <a name="transcription-delivery-and-playback"></a>Entrega e reprodução de transcrição

Reveja o artigo de [visão geral](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) da embalagem Dinâmica de como o nosso serviço utiliza embalagens dinâmicas para entregar vídeo, áudio e texto em diferentes protocolos. Quando publica o seu live stream utilizando MPEG-DASH ou HLS/CMAF, em seguida, juntamente com vídeo e áudio, o nosso serviço entrega o texto transcrito em TTML compatível com IMSC1.1. Esta entrega é embalada em fragmentos MPEG-4 Parte 30 (ISO/IEC 14496-30). Se utilizar a entrega via HLS/TS, o texto é entregue como VTT em pedaços. Pode utilizar um web player como o [Azure Media Player](use-azure-media-player.md) para reproduzir o stream.  

> [!NOTE]
> Se utilizar o Azure Media Player, utilize a versão 2.3.3 ou posterior.

## <a name="known-issues"></a>Problemas conhecidos

Para pré-visualização, são conhecidos os seguintes problemas com transcrição ao vivo:

* A funcionalidade está disponível apenas no West US 2.
* As aplicações precisam de utilizar as APIs de pré-visualização, descritas na [Especificação Do OpenAPI dos Serviços de Mídia v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* A única língua suportada é o inglês (en-us).
* Com a proteção de conteúdos, apenas a encriptação do envelope AES é suportada.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral dos Serviços de Mídia](media-services-overview.md)

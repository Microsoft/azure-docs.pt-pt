---
title: Transcrição ao vivo dos serviços de mídia do Azure | Microsoft Docs
description: Este artigo explica o que é a transcrição dinâmica dos serviços de mídia do Azure.
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
ms.openlocfilehash: a85f9f8b9d98f77cf673778f031d8f47f132fbe1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327350"
---
# <a name="live-transcription-preview"></a>Transcrição ao vivo (versão prévia)

O serviço de mídia do Azure fornece vídeo, áudio e, agora, texto em diferentes protocolos. Quando você publica sua transmissão ao vivo usando MPEG-DASH ou HLS/CMAF, juntamente com vídeo e áudio, nosso serviço fornecerá o texto transtratado no TTML compatível com IMSC 1.1, empacotado nos fragmentos de MPEG-4, parte 30 (ISO/IEC 14496-30). Se estiver usando a entrega via HLS/TS, o texto será entregue como um VTT em bloco. 

Este artigo descreve como habilitar a transcrição ao vivo ao transmitir um evento ao vivo com os serviços de mídia do Azure v3. Antes de prosseguir, verifique se você está familiarizado com o uso das APIs REST dos serviços de mídia v3 (consulte [este tutorial](stream-files-tutorial-with-rest.md) para obter detalhes). Você também deve estar familiarizado com o conceito de [transmissão ao vivo](live-streaming-overview.md) . É recomendável concluir o tutorial do [Stream Live com os serviços de mídia](stream-live-tutorial-with-api.md) . 

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como um recurso de visualização na região oeste dos EUA 2. Ele dá suporte à transcrição de palavras faladas em inglês para texto. A referência de API para esse recurso está neste documento – já que ele está em versão prévia, os detalhes não estão disponíveis com nossos documentos REST. 

## <a name="creating-the-live-event"></a>Criando o evento ao vivo 

Para criar o evento ao vivo, você enviaria a operação PUT para a versão 2019-05-01, como: 

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A operação tem o seguinte corpo (em que um evento de passagem ao vivo é criado com RTMP como o protocolo de ingestão). Observe a adição de uma propriedade transcrições. O único valor permitido para o idioma é en-US. 

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

Você deve sondar o status do evento ao vivo até que ele vá para o estado "em execução", que indica que agora você pode enviar um feed RTMP de contribuição. Agora você pode seguir as mesmas etapas deste tutorial, como verificar o feed de visualização e criar saídas dinâmicas. 

## <a name="delivery-and-playback"></a>Entrega e reprodução 

Examine o artigo [visão geral do empacotamento dinâmico](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) de como nosso serviço usa o empacotamento dinâmico para fornecer vídeo, áudio e agora texto em diferentes protocolos. Quando você publica sua transmissão ao vivo usando MPEG-DASH ou HLS/CMAF, juntamente com vídeo e áudio, nosso serviço fornecerá o texto transtratado no TTML compatível com IMSC 1.1, empacotado nos fragmentos de MPEG-4, parte 30 (ISO/IEC 14496-30). Se estiver usando a entrega via HLS/TS, o texto será entregue como um VTT em bloco. Você pode usar um Web Player, como o [player de mídia do Azure](use-azure-media-player.md) para reproduzir o fluxo.  

> [!NOTE]
>  Se estiver usando Player de Mídia do Azure, use a versão 2.3.3 ou posterior.

## <a name="known-issues"></a>Problemas conhecidos 

Na visualização, veja a seguir os problemas conhecidos com a transcrição ao vivo 

* O recurso está disponível apenas no oeste dos EUA 2.
* Os aplicativos precisam usar as APIs de visualização, descritas na especificação de [especificação openapi dos serviços de mídia v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json) .
* O único idioma com suporte é o inglês.
* Em relação à proteção de conteúdo, há suporte apenas para criptografia de envelope AES.

## <a name="next-steps"></a>Passos Seguintes

[Visão geral dos serviços de mídia](media-services-overview.md)

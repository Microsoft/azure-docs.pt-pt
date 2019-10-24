---
title: Moderação de vídeo com revisão humana-Content Moderator
titleSuffix: Azure Cognitive Services
description: Use ferramentas de análise humana e moderação de vídeo assistida por computador para conteúdo moderado inadequado
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: pafarley
ms.openlocfilehash: a4e7b079367a4b4dec1d2b3d6c0afde1d8276766
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754159"
---
# <a name="video-moderation-with-human-review"></a>Moderação de vídeo com revisão humana

Use a [ferramenta de análise humana](Review-Tool-User-Guide/human-in-the-loop.md) e [moderação de vídeo](video-moderation-api.md) assistida por computador Content moderator para vídeos moderados e transcrições para conteúdo adulto (explícito) e erótico (Sugestor) para obter os melhores resultados para seus negócios.

## <a name="video-trained-classifier-preview"></a>Classificador treinado por vídeo (versão prévia)

A classificação de vídeo assistida por computador é obtida com modelos treinados de imagem ou modelos treinados de vídeo. Diferentemente dos classificadores de vídeo treinados em imagem, o classificador de vídeo adulto e erótico da Microsoft é treinado com vídeos. Esse método resulta em melhor qualidade de correspondência.

## <a name="shot-detection"></a>Detecção de captura

Ao gerar os detalhes de classificação, a inteligência de vídeo adicional ajuda com mais flexibilidade na análise de vídeos. Em vez de gerar apenas os quadros, o serviço de moderação de vídeo da Microsoft também fornece informações de nível de captura. Agora você tem a opção de analisar seus vídeos no nível de captura e no nível de quadro.

## <a name="key-frame-detection"></a>Detecção de quadro chave

Em vez de gerar quadros em intervalos regulares, o serviço de moderação de vídeo identifica e gera somente quadros potencialmente completos (bons). O recurso permite geração de quadros eficiente para análise de adulto e erótico no nível do quadro.

A extração a seguir mostra uma resposta parcial com possíveis capturas, quadros-chave e pontuações de adulto e erótico:

```json
"fragments":[  
  {  
    "start":0,
    "duration":18000
  },
  {  
    "start":18000,
    "duration":3600,
    "interval":3600,
    "events":[  
      [  
        {  
          "reviewRecommended":false,
          "adultScore":0.00001,
          "racyScore":0.03077,
          "index":5,
          "timestamp":18000,
          "shotIndex":0
        }
      ]
    ]
  },
  {  
    "start":18386372,
    "duration":119149,
    "interval":119149,
    "events":[  
      [  
        {  
          "reviewRecommended":true,
          "adultScore":0.00000,
          "racyScore":0.91902,
          "index":5085,
          "timestamp":18386372,
          "shotIndex":62
        }
      ]
    ]
```

## <a name="visualization-for-human-reviews"></a>Visualização para revisões humanas

Para casos mais exnuances, as empresas precisam de uma solução de revisão humana para renderizar o vídeo, seus quadros e marcas de máquina atribuída. Os moderadores humanos que revisam vídeos e quadros têm uma visão completa das informações, alteram as marcas e enviam suas decisões.

![exibição padrão da ferramenta de revisão de vídeo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Exibição do Player para revisão no nível de vídeo

As decisões binárias no nível de vídeo são possíveis com uma exibição de player de vídeo que mostra os quadros de adulto e erótico em potencial. Os revisores humanos navegam pelo vídeo com várias opções de velocidade para examinar os bastidores. Eles confirmam suas decisões alternando as marcas.

![exibição do Player da ferramenta de revisão de vídeo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Exibição de quadros para revisões detalhadas

Uma revisão de vídeo detalhada para análise quadro a quadro é possibilitada com uma exibição baseada em quadros. Os revisores humanos revisam e selecionam um ou mais quadros e alternam marcas para confirmar suas decisões. Uma próxima etapa opcional é a redação de quadros ou conteúdo ofensivo.

![exibição de quadros da ferramenta de revisão de vídeo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderação de transcrições

Os vídeos normalmente têm voz sobre isso também precisa de moderação para a fala ofensiva. Você usa o serviço de Azure Media Indexer para converter a fala em texto e usar a API de revisão do Content Moderator para enviar a transcrição para moderação de texto na ferramenta de revisão.

![exibição de transcrição da ferramenta de revisão de vídeo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Passos seguintes

- Comece a usar o guia de [início rápido de moderação de vídeo](video-moderation-api.md).
- Saiba como gerar [revisões de vídeo](video-reviews-quickstart-dotnet.md) para seus revisores humanos de sua saída moderada.
- Adicione [revisões de transcrição em vídeo](video-transcript-reviews-quickstart-dotnet.md) às suas revisões de vídeo.
- Confira o tutorial detalhado sobre como desenvolver uma [solução de moderação de vídeo completa](video-transcript-moderation-review-tutorial-dotnet.md).
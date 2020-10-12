---
title: Moderação de vídeo com revisão humana - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize moderação de vídeo assistida por máquina e a ferramenta Review para moderar conteúdo inadequado
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81404244"
---
# <a name="video-moderation-with-human-review"></a>Moderação de vídeo com revisão humana

Utilize a ferramenta de [moderação](video-moderation-api.md) e [revisão](Review-Tool-User-Guide/human-in-the-loop.md) de vídeo assistida por moderadores de conteúdo para moderar vídeos e transcrições para conteúdos adultos (explícitos) e picantes (sugestivos) para obter os melhores resultados para o seu negócio.

## <a name="video-trained-classifier-preview"></a>Classificador treinado em vídeo (pré-visualização)

A classificação de vídeo assistida por máquinas é conseguida com modelos treinados por imagem ou modelos treinados em vídeo. Ao contrário dos classificadores de vídeo treinados por imagem, o classificador de vídeo adulto e picante da Microsoft é treinado com vídeos. Este método resulta numa melhor qualidade de correspondência.

## <a name="shot-detection"></a>Deteção de tiro

Ao des outputing os detalhes da classificação, a inteligência adicional de vídeo ajuda com mais flexibilidade na análise de vídeos. Em vez de passar apenas os quadros, o serviço de moderação de vídeo da Microsoft também fornece informações de nível de tiro. Tem agora a opção de analisar os seus vídeos ao nível do tiro e ao nível da moldura.

## <a name="key-frame-detection"></a>Deteção de quadros-chave

Em vez de fazer fotogramas de saída a intervalos regulares, o serviço de moderação de vídeo identifica e produz apenas quadros (bons) potencialmente completos. A funcionalidade permite uma geração de quadros eficiente para análise adulta e picante ao nível do quadro.

O seguinte extrato mostra uma resposta parcial com potenciais tiros, quadros-chave e pontuações adultas e picantes:

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

## <a name="visualization-for-human-reviews"></a>Visualização para avaliações humanas

Para casos mais matizados, as empresas precisam de uma solução de revisão humana para renderizar o vídeo, os seus quadros e tags atribuídas por máquinas. Os moderadores humanos que analisam vídeos e quadros têm uma visão completa das ideias, mudam as etiquetas e submetem as suas decisões.

![visão padrão da ferramenta de revisão de vídeo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Vista do jogador para revisão de nível de vídeo

As decisões binárias ao nível do vídeo são possíveis com uma visão do leitor de vídeo que mostra molduras potenciais para adultos e picantes. Os revisores humanos navegam no vídeo com várias opções de velocidade para examinar as cenas. Eles confirmam as suas decisões toggling as etiquetas.

![vista de jogador de ferramenta de revisão de vídeo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Vista de quadros para avaliações detalhadas

Uma análise detalhada de vídeo para análise quadro-a-quadro é possível com uma visão baseada em quadros. Os revisores humanos analisam e selecionam um ou mais quadros e alternam tags para confirmar as suas decisões. Um próximo passo opcional é a redação dos quadros ou conteúdos ofensivos.

![vista de quadros de ferramenta de revisão de vídeo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderação de transcrições

Os vídeos normalmente têm voz sobre que precisa de moderação também para discurso ofensivo. Utiliza o serviço Azure Media Indexer para converter a fala em texto e utilizar a API de revisão do Moderador de Conteúdo para submeter a transcrição para moderação de texto dentro da ferramenta de revisão.

![vista de transcrição de ferramenta de revisão de vídeo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Passos seguintes

- Começa com o arranque rápido da [moderação de vídeo](video-moderation-api.md).
- Aprenda a gerar [comentários de vídeo](video-reviews-quickstart-dotnet.md) para os seus revisores humanos a partir da sua saída moderada.
- Adicione [comentários de transcrição de vídeo](video-transcript-reviews-quickstart-dotnet.md) às suas avaliações de vídeo.
- Confira o tutorial detalhado sobre como desenvolver uma solução completa de [moderação de vídeo.](video-transcript-moderation-review-tutorial-dotnet.md)
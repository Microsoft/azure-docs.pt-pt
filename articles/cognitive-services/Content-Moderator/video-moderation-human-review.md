---
title: Moderação de vídeo com análise humana - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a moderação de vídeo assistida por máquinae a ferramenta Review para moderar conteúdo inadequado
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 0c031a890efc7fad7e5d9caefce3b0e66c515d90
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404244"
---
# <a name="video-moderation-with-human-review"></a>Moderação de vídeo com revisão humana

Utilize a ferramenta de moderação de vídeo assistida por máquina e [análise](Review-Tool-User-Guide/human-in-the-loop.md) assistida por conteúdo para [moderar](video-moderation-api.md) vídeos e transcrições para conteúdos adultos (explícitos) e picantes (sugestivos) para obter os melhores resultados para o seu negócio.

## <a name="video-trained-classifier-preview"></a>Classificador treinado em vídeo (pré-visualização)

A classificação de vídeo assistida por máquina é conseguida com modelos treinados por imagem ou modelos treinados por vídeo. Ao contrário dos classificadores de vídeo treinados por imagens, o classificador de vídeo adulto e picante da Microsoft é treinado com vídeos. Este método resulta numa melhor qualidade de correspondência.

## <a name="shot-detection"></a>Deteção de tiro

Ao distribuir os detalhes da classificação, a inteligência adicional em vídeo ajuda com mais flexibilidade na análise de vídeos. Em vez de eliminar apenas os quadros, o serviço de moderação de vídeo da Microsoft também fornece informações ao nível dos tiros. Agora tem a opção de analisar os seus vídeos ao nível da filmagem e ao nível da moldura.

## <a name="key-frame-detection"></a>Deteção de quadros chave

Em vez de eliminar quadros em intervalos regulares, o serviço de moderação de vídeo identifica e saídas apenas quadros (bons) potencialmente completos. A funcionalidade permite uma geração eficiente de quadros para análise seleção adulta e picante.

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

## <a name="visualization-for-human-reviews"></a>Visualização de críticas humanas

Para casos mais matizados, as empresas precisam de uma solução de revisão humana para renderizar o vídeo, as suas molduras e etiquetas atribuídas à máquina. Os moderadores humanos que analisam vídeos e quadros têm uma visão completa dos insights, mudam de etiquetas e submetem as suas decisões.

![visão padrão da ferramenta de revisão de vídeo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Vista do jogador para revisão de nível de vídeo

As decisões binárias de nível de vídeo são possíveis com uma visão de leitor de vídeo que mostra potenciais quadros adultos e picantes. Os revisores humanos navegam no vídeo com várias opções de velocidade para examinar as cenas. Confirmam as suas decisões toggling as etiquetas.

![visão do jogador de ferramentas de revisão de vídeo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Vista de quadros para avaliações detalhadas

Uma análise detalhada de vídeo para análise quadro a quadro é possível com uma visão baseada em quadros. Os revisores humanos analisam e selecionam um ou mais quadros e etiquetas de alternância para confirmar as suas decisões. Um próximo passo opcional é a redação dos quadros ou conteúdos ofensivos.

![visão de quadros de ferramentas de revisão de vídeo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderação de transcrições

Os vídeos normalmente têm voz sobre o que precisa de moderação também para discurso ofensivo. Utiliza o serviço Azure Media Indexer para converter a fala em texto e utilizar a Análise de Conteúdo moderador para submeter a transcrição para moderação de texto dentro da ferramenta de revisão.

![vista de transcrição de ferramenta de revisão de vídeo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Passos seguintes

- Comece com a moderação de [vídeo quickstart](video-moderation-api.md).
- Saiba como gerar [críticas](video-reviews-quickstart-dotnet.md) de vídeo para os seus revisores humanos a partir da sua saída moderada.
- Adicione [comentários de transcrição](video-transcript-reviews-quickstart-dotnet.md) de vídeo nas suas análises de vídeo.
- Confira o tutorial detalhado sobre como desenvolver uma [solução completa](video-transcript-moderation-review-tutorial-dotnet.md)de moderação de vídeo .
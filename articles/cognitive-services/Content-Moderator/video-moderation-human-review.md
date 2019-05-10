---
title: Moderação de vídeos com revisão humana - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilize a moderação de vídeos assistida e ferramentas de revisão humana para moderar conteúdo inadequado
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: sajagtap
ms.openlocfilehash: a6c467d3153400815e37a5d461766140abd1fa32
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228122"
---
# <a name="video-moderation-with-human-review"></a>Moderação de vídeos com revisão humana

Utilize Content Moderator assistida [moderação de vídeos](video-moderation-api.md) e [ferramenta de revisão humana](Review-Tool-User-Guide/human-in-the-loop.md) para vídeos moderados e transcrições para adulto (explícita) e o conteúdo para adultos de (suggestive) para obter os melhores resultados para sua empresa.

## <a name="video-trained-classifier-preview"></a>Classificador treinados de vídeo (pré-visualização)

Classificação de vídeo assistida optar por é obtida com modelos treinados de imagens ou modelos de formação de vídeo. Ao contrário de classificadores de preparação de imagem de vídeo, classificador de vídeo para adultos da Microsoft é preparado com vídeos. Este método resulta em melhor qualidade de correspondência.

## <a name="shot-detection"></a>Deteção de captura

Na saída dos detalhes de classificação, ajuda a inteligência adicional de vídeo com mais flexibilidade na análise de vídeos. Em vez de saída apenas os quadros, o serviço de moderação de vídeo da Microsoft fornece informações de nível de captura demasiado. Tem agora a opção de analisar os seus vídeos em nível de captura e o nível de quadro.

## <a name="key-frame-detection"></a>Deteção de quadro-chave

Em vez de saída de quadros em intervalos regulares, o serviço de moderação de vídeos identifica e produz apenas potencialmente completos quadros (boas). A funcionalidade permite a geração de quadro eficiente para análise de nível de quadro para adultos.

O extrato seguinte mostra uma resposta parcial com capturas de potenciais, quadros-chave e as pontuações de adultos:

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

## <a name="visualization-for-human-reviews"></a>Visualização de revisões realizadas por pessoas

Para obter mais variada casos, as empresas precisam de uma solução de revisão humana para compor o vídeo, seus quadros e etiquetas de máquina atribuída. Os revisão de vídeos e quadros de moderadores humanos obtém uma visão completa das informações, alterar etiquetas e submeter as suas decisões.

![modo de exibição de padrão de ferramenta de revisão de vídeo](images/video-review-default-view.png)

## <a name="player-view-for-video-level-review"></a>Vista de Player para revisão de ao nível do vídeo

Decisões binárias de nível de vídeo são possíveis com uma vista de leitor de vídeo que mostra o potenciais quadros para adultos. Os revisores humanos navegue até o vídeo com várias opções de velocidade para examinar o plano. Eles confirmarem suas decisões, Ativando as etiquetas.

![exibição de leitor de ferramentas de revisão de vídeo](images/video-review-player-view.PNG)

## <a name="frames-view-for-detailed-reviews"></a>Vista de quadros para análises detalhadas

Uma análise detalhada de vídeo para análise de quadro por quadro se tornou possível com uma vista baseada no quadro. Os revisores humanos reverem e selecionar um ou mais quadros e alternar etiquetas para confirmar as suas decisões. Uma próxima etapa opcional é redação de quadros ofensivos ou do conteúdo.

![exibição de quadros de ferramentas de revisão de vídeo](images/video-review-frames-view-apply-tags.PNG)

## <a name="transcript-moderation"></a>Moderação de transcrições

Vídeos têm normalmente voz através de que precisa de moderação também para voz ofensiva. Utilize o serviço de indexador de multimédia do Azure para converter voz em texto e utilizar a API de revisão do Content Moderator para submeter a transcrição de moderação de texto dentro da ferramenta de revisão.

![exibição de transcrição de ferramentas de revisão de vídeo](images/video-review-transcript-view.png)

## <a name="next-steps"></a>Passos Seguintes

- Comece com o [guia de introdução de moderação de vídeos](video-moderation-api.md).
- Saiba como gerar [revisões de vídeo](video-reviews-quickstart-dotnet.md) para os revisores humanos da sua saída moderada.
- Adicione [revisões de transcrição de vídeo](video-transcript-reviews-quickstart-dotnet.md) para suas revisões de vídeo.
- Veja o tutorial detalhado sobre como desenvolver um [concluir a solução de moderação de vídeos](video-transcript-moderation-review-tutorial-dotnet.md).
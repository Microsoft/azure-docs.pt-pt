---
title: Conceitos de Indexante de Vídeo - Azure
titleSuffix: Azure Media Services Video Indexer
description: Este artigo apresenta uma breve visão geral da terminologia e conceitos do Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 41c9dfe9251da3bddb16ff507ebd512713c3b88a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633985"
---
# <a name="video-indexer-concepts"></a>Conceitos de Indexer de Vídeo

Este artigo apresenta uma breve visão geral da terminologia e conceitos do Azure Media Services Video Indexer.

## <a name="audiovideocombined-insights"></a>Insights áudio/vídeo/combinados

Ao fazer o upload dos seus vídeos para o Video Indexer, analisa tanto os visuais como o áudio, executando diferentes modelos de IA. À medida que o Video Indexer analisa o seu vídeo, as insights que são extraídas pelos modelos de IA. Para mais informações, consulte [a visão geral.](video-indexer-overview.md)

## <a name="confidence-scores"></a>Pontuações de confiança

A pontuação de confiança indica a confiança numa visão. É um número entre 0,0 e 1.0. Quanto maior for a pontuação, maior a confiança na resposta. Por exemplo, 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>Moderação de conteúdo

Utilize modelos de moderação de conteúdo sonoro e visual para manter os seus utilizadores a salvo de conteúdos inadequados e valide que o conteúdo que publica corresponde aos valores da sua organização. Pode bloquear automaticamente determinados vídeos ou alertar os seus utilizadores sobre o conteúdo. Para obter mais informações, consulte [Insights: moderação de conteúdo visual e textual](video-indexer-output-json-v2.md#visualcontentmoderation). 

## <a name="blocks"></a>Blocos   

Os blocos são feitos para facilitar a análise dos dados. Por exemplo, o bloco pode estar dividido com base nos momentos em que ocorre uma mudança de orador ou uma longa pausa.  

## <a name="project-and-editor"></a>Projeto e editor

O website [Video Indexer](https://www.videoindexer.ai/) permite-lhe utilizar as informações profundas do seu vídeo para: encontrar o conteúdo certo dos meios de comunicação, localizar as partes que lhe interessam e utilizar os resultados para criar um projeto inteiramente novo. Uma vez criado, o projeto pode ser renderizado e descarregado a partir de Video Indexer e ser usado nas suas próprias aplicações de edição ou fluxos de trabalho a jusante.

Alguns cenários onde poderá achar este recurso útil são: 

* Criando destaques de filmes para trailers.
* Usando velhos clips de vídeos em noticiários.
* Criar conteúdo mais curto para as redes sociais.

Para obter mais informações, consulte [o editor use para criar projetos.](use-editor-create-project.md)

## <a name="keyframes"></a>Quadros-chave

O Indexante de Vídeo seleciona o quadro(s) que melhor representa cada tiro. Os quadros-chave são os quadros representativos selecionados de todo o vídeo com base em propriedades estéticas (por exemplo, contraste e estável). Para mais informações, consulte [Cenas, fotos e quadros-chave.](scenes-shots-keyframes.md)

## <a name="time-range-vs-adjusted-time-range"></a>intervalo de tempo vs. intervalo de tempo ajustado   

TimeRange é o intervalo de tempo no vídeo original. OTimeRange ajustado é o intervalo de tempo em relação à lista de reprodução atual. Uma vez que pode criar uma lista de reprodução a partir de diferentes linhas de diferentes vídeos, pode pegar num vídeo de 1 hora e usar apenas uma linha a partir dele, por exemplo, 10:00-10:15. Nesse caso, terá uma lista de reprodução com 1 linha, onde o intervalo de tempo é 10:00-10:15, mas oTimeRange ajustado é 00:00-00:15. 

## <a name="widgets"></a>Widgets

O Video Indexer suporta a incorporação de widgets nas suas aplicações. Para obter mais informações, consulte [os widgets do Indexer de Vídeo Incorporado nas suas aplicações.](video-indexer-embed-widgets.md)

## <a name="summarized-insights"></a>Insights resumidos  

Os insights resumidos contêm uma visão agregada dos dados: rostos, tópicos, emoções. Por exemplo, em vez de passar por cima de cada um dos milhares de intervalos de tempo e verificar quais as faces que estão nele, os conhecimentos resumidos contêm todos os rostos e, para cada um, os intervalos de tempo em que aparece e a % do tempo em que é mostrado.  

## <a name="next-steps"></a>Passos seguintes

- [descrição geral](video-indexer-overview.md)
- [Informações](video-indexer-output-json-v2.md)

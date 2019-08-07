---
title: Video Indexer cenas, capturas e quadros-chave – Azure
titlesuffix: Azure Media Services
description: Este tópico fornece uma visão geral das Video Indexer cenas, capturas e quadros-chave.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815647"
---
# <a name="scenes-shots-and-keyframes"></a>Cenas, capturas e keyframes

Video Indexer dá suporte ao segmentação de vídeos em unidades temporais com base nas propriedades estruturais e semânticas. Essa funcionalidade permite que os clientes naveguem, gerenciem e editem com facilidade seu conteúdo de vídeo com base em diferentes granularidades. Por exemplo, com base em cenas, capturas e quadros-chave, descritos neste tópico.   

![Cenas, capturas e keyframes](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>Detecção de cena  
 
Video Indexer determina quando uma cena é alterada em vídeo com base em indicações visuais. Uma cena descreve um único evento e é composta de uma série de capturas consecutivas, que são relacionadas semanticamente. Uma miniatura de cena é o primeiro quadro-chave de sua captura subjacente. O video indexer segmenta um vídeo em cenas com base em coerência de cores em capturas consecutivas e recupera a hora de início e de término de cada cena. A detecção de cena é considerada uma tarefa desafiadora, pois envolve a quantificação de aspectos semânticos de vídeos.

> [!NOTE]
> Aplicável a vídeos que contêm pelo menos 3 cenas.

## <a name="shot-detection"></a>Detecção de captura

Video Indexer determina quando uma captura é alterada no vídeo com base em indicações visuais, rastreando transições abruptas e graduais no esquema de cores de quadros adjacentes. Os metadados da captura incluem uma hora de início e de término, bem como a lista de quadros-chave incluídos nessa captura. As capturas são quadros consecutivos tirados da mesma câmera ao mesmo tempo.

## <a name="keyframe-detection"></a>Detecção de quadro-chave

Seleciona os quadros que melhor representam a captura. Quadros-chave são os quadros representativos selecionados de todo o vídeo com base em Propriedades estética (por exemplo, contraste e capacidade de ativação). Video Indexer recupera uma lista de IDs de quadro-chave como parte dos metadados da captura, com base nas quais os clientes podem extrair a miniatura do quadro-chave. 

Os quadros-chave são associados a capturas no JSON de saída. 

## <a name="next-steps"></a>Passos Seguintes

[Examinar a saída de Video Indexer produzida pela API](video-indexer-output-json-v2.md#scenes)

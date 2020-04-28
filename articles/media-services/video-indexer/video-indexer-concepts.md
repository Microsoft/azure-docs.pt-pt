---
title: Conceitos de Indexer de Vídeo
titleSuffix: Azure Media Services
description: Este artigo descreve alguns conceitos do serviço Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 73dad1db4f44134f871c9f3d6e7edcdd3bd1e2ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900677"
---
# <a name="video-indexer-concepts"></a>Conceitos de Indexer de Vídeo
 
Este artigo descreve alguns conceitos do serviço Indexer de Vídeo.
    
## <a name="summarized-insights"></a>Insights resumidos

Insights resumidos contêm uma visão agregada dos dados: rostos, tópicos, emoções. Por exemplo, em vez de analisar cada um dos milhares de intervalos de tempo e verificar quais as faces que estão nele, os conhecimentos resumidos contêm todos os rostos e, para cada um, o intervalo de tempo em que aparece e a % do tempo em que é mostrado.

## <a name="time-range-vs-adjusted-time-range"></a>intervalo de tempo vs. intervalo de tempo ajustado

TimeRange é o intervalo de tempo no vídeo original. O TimeRange ajustado é o intervalo de tempo relativo à lista de reprodução atual. Uma vez que pode criar uma lista de reprodução de diferentes linhas de diferentes linhas de vídeos diferentes, pode pegar num vídeo de 1 hora e usar apenas 1 linha, por exemplo, das 10:00-10:15. Nesse caso, terá uma lista de reprodução com 1 linha, onde o intervalo de tempo é 10:00-10:15, mas o TimeRange ajustado é 00:00-00:15.
 
## <a name="blocks"></a>Blocos

Os blocos destinam-se a facilitar a análise dos dados. Por exemplo, o bloco pode estar dividido com base nos momentos em que ocorre uma mudança de orador ou uma longa pausa.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como começar, consulte [como se inscrever e fazer upload do seu primeiro vídeo](video-indexer-get-started.md).

## <a name="see-also"></a>Consulte também

[Descrição geral do Video Indexer](video-indexer-overview.md)

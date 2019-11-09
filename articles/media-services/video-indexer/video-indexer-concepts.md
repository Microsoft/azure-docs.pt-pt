---
title: Conceitos de Video Indexer
titleSuffix: Azure Media Services
description: Este tópico descreve alguns conceitos do serviço de Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: e3795b699f5518efdbef117fb24d118041ed3e78
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839567"
---
# <a name="video-indexer-concepts"></a>Conceitos de Video Indexer
 
Este artigo descreve alguns conceitos do serviço de Video Indexer.
    
## <a name="summarized-insights"></a>Informações resumidas

As informações resumidas contêm uma exibição agregada dos dados: rostos, tópicos, emoções. Por exemplo, em vez de passar por cada um dos milhares de intervalos de tempo e verificar quais faces estão nele, as informações resumidas contêm todas as faces e, para cada uma delas, os intervalos de tempo em que aparecem e o% do tempo em que é mostrado.

## <a name="time-range-vs-adjusted-time-range"></a>intervalo de tempo versus ajustado

Timerange é o intervalo de tempo no vídeo original. AdjustedTimeRange é o intervalo de tempo relativo à playlist atual. Como você pode criar uma lista de reprodução de diferentes linhas de vídeos diferentes, você pode levar um vídeo de 1 hora e usar apenas uma linha dela, por exemplo, 10:00-10:15. Nesse caso, você terá uma lista de reprodução com 1 linha, em que o intervalo de tempo é 10:00-10:15, mas o adjustedTimeRange é 00:00-00:15.
 
## <a name="blocks"></a>trava

Os blocos destinam-se a facilitar o uso dos dados. Por exemplo, o bloco pode estar dividido com base nos momentos em que ocorre uma mudança de orador ou uma longa pausa.

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como começar, consulte [como inscrever-se e carregar seu primeiro vídeo](video-indexer-get-started.md).

## <a name="see-also"></a>Consultar também

[Descrição geral do Video Indexer](video-indexer-overview.md)

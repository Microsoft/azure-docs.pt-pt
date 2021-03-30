---
title: Gravação de vídeo - Azure
description: No contexto de um sistema de gestão de vídeo para câmaras CCTV, a gravação de vídeo refere-se ao processo de captura de vídeo das câmaras e gravação do mesmo para visualização através de aplicações móveis e de navegador. A gravação de vídeo pode ser classificada como gravação contínua de vídeo e gravação de vídeo baseada em eventos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84260947"
---
# <a name="video-recording"></a>Gravação de vídeo

No contexto de um sistema de gestão de vídeo para câmaras CCTV, a gravação de vídeo refere-se ao processo de captura de vídeo das câmaras e gravação do mesmo para visualização através de aplicações móveis e de navegador. A gravação de vídeo pode ser classificada como gravação contínua de vídeo e gravação de vídeo baseada em eventos. 

## <a name="continuous-video-recording"></a>Gravação de vídeo contínua  

Gravação contínua de vídeo (CVR) refere-se ao processo de gravação contínua de todo o vídeo capturado a partir de uma fonte de vídeo. A CVR garante que todo o vídeo está disponível (ditado pela [política de gravação)](#recording-policy)para analisar e/ou auditar mais tarde.

## <a name="event-based-video-recording"></a>Gravação de vídeo baseada em eventos  

A gravação de vídeo baseada em eventos (EVR) refere-se ao processo de gravação de vídeo desencadeado por um evento. O evento em questão, pode ter origem no processamento do próprio sinal de vídeo ou pode ser de uma fonte independente (por exemplo, de um sensor da porta). A gravação de vídeo baseada em eventos (EVR) pode resultar em poupança de armazenamento, mas requer componentes adicionais que gerem os eventos e desencadeiam a gravação de vídeo (de acordo com uma política pré-definida). Por outras palavras, a EVR requer tomar decisões adicionais sobre os eventos que devem desencadear a gravação de vídeo e a política associada à gravação de vídeo (também conhecida como política de gravação). Um exemplo de uma política pode ser algo como: gravar o vídeo durante 2 minutos a partir de 30 segundos antes da hora do evento. Os acontecimentos em questão podem ter origem devido ao processamento de vídeo na própria câmara. Como exemplo, várias câmaras suportam gerar um evento de sinal de deteção de movimento quando o movimento é detetado dentro de uma zona de interesse pré-configurada no viewport da câmara. Os eventos também poderiam ser gerados através do processamento do vídeo num outro dispositivo que captura o vídeo da câmara e o analisa usando técnicas simples de processamento de imagem ou modelos sofisticados de machine learning. 

## <a name="choosing-recording-modes"></a>Escolher modos de gravação  

A escolha de usar CVR ou EVR depende dos objetivos do negócio. Live Video Analytics on IoT Edge fornece capacidades de plataforma tanto para CVR como EVR. Pode saber mais sobre isso nos artigos de cenário [CVR](continuous-video-recording-concept.md) e [EVR.](event-based-video-recording-concept.md)

## <a name="recording-policy"></a>Política de gravação  

A política de gravação refere-se às políticas que ditam o tempo de início/paragem da gravação (no caso de EVR), a duração da gravação e a eliminação da gravação. As políticas de gravação permitem equilibrar os custos de armazenamento com os requisitos do negócio. As políticas de gravação diferem entre CVR e EVR. Para o CVR, a política de gravação define quantos dias de vídeo devem ser armazenados (por exemplo, os últimos 7 dias). Para o EVR, a política de gravação define quando a gravação deve começar e terminar, juntamente com a duração do vídeo. Pode saber mais sobre isso nos artigos de cenário [CVR](continuous-video-recording-concept.md) e [EVR.](event-based-video-recording-concept.md)

## <a name="next-steps"></a>Passos seguintes

* [Detete movimento e grave clips de vídeo para os Serviços de Multimédia do Azure](detect-motion-record-video-clips-media-services-quickstart.md)
* [Detetar movimento, gravar videoclips para dispositivos de borda](detect-motion-record-video-clips-edge-devices-quickstart.md)


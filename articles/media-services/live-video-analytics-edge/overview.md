---
title: What is Live Video Analytics on IoT Edge - Azure
description: Este tópico fornece uma visão geral do Live Video Analytics no IoT Edge. A plataforma oferece a capacidade que pode usar para melhorar as suas soluções IoT. Por exemplo, capturar, gravar, analisar vídeo ao vivo e publicar os resultados (análise de vídeo e/ou vídeo) aos serviços Azure.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: 81a67322d0a5e524d75b9bf3e481be7157e09e63
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266802"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>O que é Live Video Analytics no IoT Edge? (pré-visualização)

Live Video Analytics on IoT Edge fornece uma plataforma para construir aplicações de vídeo inteligentes que se estendem pela borda e pela nuvem. A plataforma oferece a capacidade de capturar, gravar e analisar vídeos ao vivo juntamente com a publicação dos resultados (vídeo e/ou análise de vídeo) aos serviços Azure (na nuvem e/ou na borda). A plataforma pode ser usada para melhorar soluções IoT com análise de vídeo. O Live Video Analytics na funcionalidade IoT Edge pode ser combinado com outros módulos Azure IoT Edge, tais como Stream Analytics no IoT Edge, Serviços Cognitivos no IoT Edge, bem como serviços Azure na nuvem como Media Services, Event Hub, Cognitive Services, etc. para construir aplicações híbridas poderosas (por exemplo, edge + cloud).

Live Video Analytics on IoT Edge foi projetado para ser uma plataforma extensível, permitindo-lhe ligar diferentes módulos de borda de análise de vídeo (como recipientes de serviços cognitivos, módulos de borda personalizada construídos por si com modelos de aprendizagem automática de código aberto ou modelos personalizados treinados com os seus próprios dados) e usá-los para analisar vídeo ao vivo sem se preocupar com a complexidade de construir e executar um pipeline de vídeo ao vivo.

## <a name="accelerate-iot-solutions-development"></a>Acelerar o desenvolvimento de soluções IoT 

As soluções IoT que combinam análises de vídeo com sinais de outros sensores IoT e/ou dados empresariais podem ajudá-lo a automatizar ou semi-automatizar decisões de negócio, resultando em melhorias de produtividade. O Live Video Analytics no IoT Edge permite-lhe construir essas soluções mais rapidamente. Pode focar-se na construção dos módulos de análise de vídeo e lógica específica para o seu negócio, e deixar a plataforma esconder as complexidades de gerir e executar um pipeline de vídeo.

Com o Live Video Analytics no IoT Edge, pode continuar a utilizar as suas [câmaras CCTV](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) com os seus sistemas de [gestão de vídeo (VMS) existentes](https://en.wikipedia.org/wiki/Video_management_system) e construir aplicações de análise de vídeo de forma independente. O Live Video Analytics on IoT Edge pode ser usado em conjunto com SDKs de visão computacional e kits de ferramentas para construir soluções IoT de ponta. O diagrama abaixo ilustra isto.

![Desenvolver soluções IoT com Análise de Vídeo Ao Vivo no IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Ambientes apoiados

Os ambientes Linux AMD64 e X64 são suportados.

## <a name="get-started"></a>Introdução

Leia os seguintes artigos de conceito e, em seguida, tente um quickstart para executar a deteção de movimentos em um feed de vídeo ao vivo.

### <a name="concepts"></a>Conceitos

* [Gráfico de mídia](media-graph-concept.md)
* [Gravação de vídeo](video-recording-concept.md)
* [Reprodução de vídeo](video-playback-concept.md)
* [Gravação contínua de vídeo](continuous-video-recording-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Análise de vídeo ao vivo sem gravação de vídeo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Passos seguintes

* Siga o [Quickstart: Executar Live Video Analytics com o seu próprio](use-your-model-quickstart.md) artigo de modelo para ver como pode executar a deteção de movimentos num feed de vídeo ao vivo.
* Rever [terminologia](terminology.md)
* Explore [o material de código aberto live video Analytics](https://github.com/Azure/live-video-analytics)


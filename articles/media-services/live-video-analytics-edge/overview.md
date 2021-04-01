---
title: What is Live Video Analytics on IoT Edge - Azure
description: Este tópico fornece uma visão geral do Live Video Analytics no IoT Edge. A plataforma oferece a capacidade que pode usar para melhorar as suas soluções IoT. Por exemplo, capturar, gravar, analisar vídeo ao vivo e publicar os resultados (análise de vídeo e/ou vídeo) aos serviços Azure.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: b9b8726c5e8ad6850e05aeee48fccabee703080e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90904349"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>O que é Live Video Analytics no IoT Edge? (pré-visualização)

O Live Video Analytics no IoT Edge proporciona uma plataforma para criar aplicações de vídeo inteligentes que abrangem o Edge e a Cloud. A plataforma oferece a capacidade de capturar, gravar e analisar vídeos em direto, além de poder publicar os resultados (vídeo e/ou análise do vídeo) nos serviços do Azure (na cloud e/ou no Edge). A plataforma pode ser usada para melhorar soluções IoT com análise de vídeo. O Live Video Analytics na funcionalidade IoT Edge pode ser combinado com outros módulos Azure IoT Edge, tais como Stream Analytics no IoT Edge, Serviços Cognitivos no IoT Edge, bem como serviços Azure na nuvem como Media Services, Event Hub, Cognitive Services, etc. para construir aplicações híbridas poderosas (por exemplo, edge + cloud).

Live Video Analytics on IoT Edge foi projetado para ser uma plataforma extensível, permitindo-lhe ligar diferentes módulos de borda de análise de vídeo (como recipientes de serviços cognitivos, módulos de borda personalizada construídos por si com modelos de aprendizagem automática de código aberto ou modelos personalizados treinados com os seus próprios dados) e usá-los para analisar vídeo ao vivo sem se preocupar com a complexidade de construir e executar um pipeline de vídeo ao vivo.

## <a name="accelerate-iot-solutions-development"></a>Acelerar o desenvolvimento de soluções IoT 

As soluções IoT que combinam a análise de vídeo com sinais de outros sensores IoT e/ou dados empresariais podem ajudar a automatizar ou semiautomatizar as decisões empresariais, o que resulta em melhorias ao nível da produtividade. O Live Video Analytics no IoT Edge permite-lhe criar estas soluções de forma mais rápida. Pode focar-se na criação de módulos de análise de vídeo e lógica específicos para a sua empresa e deixar que a plataforma oculte as complexidades da gestão e execução de um pipeline de vídeo.

Com o Live Video Analytics no IoT Edge, pode continuar a utilizar as suas [câmaras CCTV](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) com os seus sistemas de [gestão de vídeo (VMS) existentes](https://en.wikipedia.org/wiki/Video_management_system) e construir aplicações de análise de vídeo de forma independente. Pode utilizar o Live Video Analytics no IoT Edge em conjunto com os SDKs e os toolkits das imagens digitalizadas para criar soluções IOT topo de gama. O diagrama abaixo ilustra isso.

![Desenvolver soluções IoT com Análise de Vídeo Ao Vivo no IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Ambientes apoiados

Os ambientes Linux x86-64 e ARM64 são suportados.
> [!NOTE]
> O suporte para dispositivos ARM64 está disponível em construções `1.0.4` e mais recentes.
> O suporte para executar o tempo de funcionamento do Azure IoT Edge em dispositivos ARM64 está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-started"></a>Introdução

Leia os seguintes artigos de conceito e, em seguida, tente um quickstart para executar a deteção de movimentos em um feed de vídeo ao vivo.

### <a name="concepts"></a>Conceitos

* [Grafo do suporte de dados](media-graph-concept.md)
* [Gravação de vídeo](video-recording-concept.md)
* [Reproduzir vídeo](video-playback-concept.md)
* [Gravação de vídeo contínua](continuous-video-recording-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)
* [Live Video Analytics sem gravação de vídeo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Passos seguintes

* Siga o [Quickstart: Executar Live Video Analytics com o seu próprio](use-your-model-quickstart.md) artigo de modelo para ver como pode executar a deteção de movimentos num feed de vídeo ao vivo.
* Rever [terminologia](terminology.md)
* Explore [o material de código aberto live video Analytics](https://github.com/Azure/live-video-analytics)


---
title: Azure IoT Central de análise de vídeo e deteção de movimentos Microsoft Docs
description: Aprenda a construir uma aplicação IoT Central usando o modelo de aplicação de deteção de objetos e movimentos no IoT Central. Este modelo utiliza análises de vídeo ao vivo e câmaras conectadas.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874293"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Análise de vídeo - arquitetura de aplicação de deteção de objetos e movimentos

O **modelo de aplicação de deteção de objetos e movimentos de vídeo permite-lhe** construir soluções IoT, incluindo capacidades de análise de vídeo ao vivo.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Diagrama de objetos de análise de vídeo e componentes de deteção de movimento.":::

Os componentes-chave da solução de análise de vídeo incluem:

## <a name="live-video-analytics-lva"></a>Análise de vídeo ao vivo (LVA)

A LVA fornece uma plataforma para construir aplicações de vídeo inteligentes que se estendem pela borda e pela nuvem. A plataforma permite-lhe construir aplicações de vídeo inteligentes que se estendem pela borda e pela nuvem. A plataforma oferece a capacidade de capturar, gravar, analisar vídeos ao vivo e publicar os resultados, que podem ser de vídeo ou vídeo, para os serviços Azure. Os serviços Azure podem estar a funcionar na nuvem ou na borda. A plataforma pode ser usada para melhorar soluções IoT com análise de vídeo.

Para mais informações, consulte [o Live Video Analytics](https://github.com/Azure/live-video-analytics) no GitHub.

## <a name="iot-edge-lva-gateway-module"></a>Módulo de gateway IoT Edge LVA

O módulo de gateway IoT Edge LVA instantaneamente as câmaras como novos dispositivos e liga-as diretamente à IoT Central utilizando o cliente do dispositivo IoT SDK.

Nesta implementação de referência, os dispositivos ligam-se à solução utilizando teclas simétricas a partir da borda. Para obter mais informações sobre a conectividade do dispositivo, consulte [Consulte Get connected to Azure IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>Grafo do suporte de dados

O gráfico de mídia permite definir de onde capturar os meios de comunicação, como processá-lo e onde entregar os resultados. Configura gráficos de mídia ligando componentes, ou nós, da maneira desejada. Para mais informações, consulte [Media Graph](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) no GitHub.

## <a name="next-steps"></a>Passos seguintes

O próximo passo sugerido é aprender como [implementar uma aplicação IoT Central usando o modelo de aplicação de vídeo - objeto e deteção de movimentos](tutorial-video-analytics-deploy.md).

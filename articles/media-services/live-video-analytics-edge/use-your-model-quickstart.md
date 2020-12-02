---
title: Analise o vídeo ao vivo utilizando o seu próprio modelo HTTP - Azure
description: Neste arranque rápido, aplicará visão computacional para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada) utilizando o seu próprio modelo HTTP.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 8e3f0a5cb6a97ce55d848c2d3311ebab5151e186
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494992"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-http-model"></a>Quickstart: Analise o vídeo ao vivo utilizando o seu próprio modelo HTTP

Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar um feed de vídeo ao vivo a partir de uma câmara IP (simulada). Verá como aplicar um modelo de visão computacional para detetar objetos. Um subconjunto dos quadros do feed de vídeo ao vivo é enviado para um serviço de inferência. Os resultados são enviados para o IoT Edge Hub. 

Este quickstart usa um Azure VM como um dispositivo IoT Edge, e utiliza um stream de vídeo simulado ao vivo. Baseia-se no código de amostra escrito em C#, e baseia-se no [movimento Detect e emite eventos](detect-motion-emit-events-quickstart.md) rápidos. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-http-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-http-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/analyze-live-video-your-http-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Descrição geral

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-http-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Criar e implementar o gráfico de mídia

::: zone pivot="programming-language-csharp"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create-deploy-media-graph](includes/analyze-live-video-your-http-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretar os resultados

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/analyze-live-video-your-http-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende tentar outros quickstarts, mantenha os recursos que criou. Caso contrário, vá ao portal Azure, vá aos seus grupos de recursos, selecione o grupo de recursos onde executou este quickstart e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

* Experimente uma [versão segura do modelo YoloV3](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) e desloque-o para o dispositivo IoT Edge. 

Rever desafios adicionais para utilizadores avançados:

* Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) que tenha suporte para RTSP em vez de utilizar o simulador RTSP. Pode pesquisar por câmaras IP que suportem RTSP na página de produtos [conformantes ONVIF.](https://www.onvif.org/conformant-products/) Procure dispositivos em conformidade com os perfis G, S ou T.
* Utilize um dispositivo Linux AMD64 ou x64 em vez de um VM Azure Linux. Este dispositivo deve estar na mesma rede que a câmara IP. Pode seguir as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Em seguida, registe o dispositivo com o Azure IoT Hub seguindo as instruções no [Implementar o seu primeiro módulo IoT Edge num dispositivo Linux virtual](../../iot-edge/quickstart-linux.md).
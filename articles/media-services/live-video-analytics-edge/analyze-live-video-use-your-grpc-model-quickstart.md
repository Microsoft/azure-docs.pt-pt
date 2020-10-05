---
title: Analise o vídeo ao vivo usando o seu próprio modelo gRPC - Azure
description: Neste arranque rápido, aplicará visão computacional para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada).
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 907d402a4b0ef5d5e437322d02431a0ccd2d8da3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89421540"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model"></a>Quickstart: Analise o vídeo ao vivo usando o seu próprio modelo gRPC

Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar um feed de vídeo ao vivo a partir de uma câmara IP (simulada). Verá como aplicar um modelo de visão computacional para detetar objetos. Um subconjunto dos quadros do feed de vídeo ao vivo é enviado para um serviço de inferência. Os resultados são enviados para o IoT Edge Hub.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Descrição geral

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Criar e implementar o gráfico de mídia

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deploy the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretar os resultados

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar recursos

Se pretende tentar outros quickstarts, mantenha os recursos que criou. Caso contrário, vá ao portal Azure, vá aos seus grupos de recursos, selecione o grupo de recursos onde executou este quickstart e elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

* Tente executar diferentes topologias de gráficos de mídia utilizando o protocolo gRPC.
* **Construir e executar extensões de vídeo ao vivo analytics (LVA)**
<br/>Experimente os nossos cadernos de amostras Jupyter que lhe permitem construir e executar modelos YOLO baseados [em ONNX](http://onnx.ai/) como extensão Live Video Analytics (LVA).
    * [Modelo yolov3 da amostra](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/readme.md)
    * [Modelo yolov4 da amostra](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov4/yolov4-grpc-icpu-onnx/readme.md)


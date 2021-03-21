---
title: Detete o movimento & gravar vídeo em dispositivos de borda - Azure
description: Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada), detetar se algum movimento está presente e, em caso afirmativo, gravar um videoclip MP4 para o sistema de ficheiros local no dispositivo de borda.
ms.topic: quickstart
ms.date: 04/27/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: e4334cdd14242337a2a870f31886606020654685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498358"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Quickstart: Detetar movimento e gravar vídeo em dispositivos de borda
 
Este quickstart mostra-lhe como usar o Live Video Analytics no IoT Edge para analisar o feed de vídeo ao vivo a partir de uma câmara IP (simulada). Mostra como detetar se algum movimento está presente e, em caso afirmativo, grava um videoclip MP4 para o sistema de ficheiros local no dispositivo de borda. O quickstart usa um Azure VM como um dispositivo IoT Edge e também usa um stream de vídeo simulado ao vivo. 

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Reveja o vídeo da amostra

::: zone pivot="programming-language-csharp"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review-sample-video](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Descrição geral

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/overview.md)]
::: zone-end

## <a name="examine-and-edit-the-sample-files"></a>Examine e edite os ficheiros de amostra

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/examine-edit-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-edit-sample-files](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/examine-edit-sample-files.md)]
::: zone-end

## <a name="review---check-the-modules-status"></a>Revisão - Verifique o estado dos módulos

::: zone pivot="programming-language-csharp"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/check-modules-status.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [check-modules-status](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/check-modules-status.md)]
::: zone-end

## <a name="review---prepare-for-monitoring-events"></a>Revisão - Preparar para eventos de monitorização

::: zone pivot="programming-language-csharp"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/prepare-monitoring-events.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prepare-monitoring-events](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/prepare-monitoring-events.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Executar o programa de amostragem

::: zone pivot="programming-language-csharp"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/run-sample-program.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [run-sample-program](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/run-sample-program.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretar os resultados 

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret-results](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="play-the-mp4-clip"></a>Toque o clipe MP4

::: zone pivot="programming-language-csharp"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/csharp/play-mp4-clip.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [play-mp4-clip](includes/detect-motion-record-video-clips-edge-devices-quickstart/python/play-mp4-clip.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretende experimentar os outros quickstarts, então mantenha os recursos que criou. Caso contrário, no portal Azure, vá aos seus grupos de recursos, selecione o grupo de recursos onde executou este arranque rápido e, em seguida, elimine todos os recursos.

## <a name="next-steps"></a>Passos seguintes

* Siga o [Run Live Video Analytics com o seu próprio modelo](use-your-model-quickstart.md) quickstart para aplicar IA em feeds de vídeo ao vivo.
* Rever desafios adicionais para utilizadores avançados:

    * Utilize uma [câmara IP](https://en.wikipedia.org/wiki/IP_camera) que suporte o RTSP em vez de utilizar o simulador RTSP. Pode encontrar câmaras IP que suportam RTSP na página de [produtos conformantes ONVIF.](https://www.onvif.org/conformant-products) Procure dispositivos em conformidade com os perfis G, S ou T.
    * Utilize um dispositivo Linux AMD64 ou x64 em vez de utilizar um Linux VM em Azure. Este dispositivo deve estar na mesma rede que a câmara IP. Siga as instruções no [tempo de funcionamento do Azure IoT Edge no Linux](../../iot-edge/how-to-install-iot-edge.md). Em seguida, siga as instruções no [Implementar o seu primeiro módulo IoT Edge num dispositivo Linux virtual](../../iot-edge/quickstart-linux.md) para registar o dispositivo com o Azure IoT Hub.
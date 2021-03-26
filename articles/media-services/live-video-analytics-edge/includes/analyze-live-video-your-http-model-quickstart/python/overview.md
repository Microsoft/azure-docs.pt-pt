---
ms.openlocfilehash: 8124f31213de1bbb794968c616e6efb940ac36b2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582557"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Fluxo de sinais":::

Este diagrama mostra como os sinais fluem neste arranque rápido. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor Real-Time de Protocolo de Streaming (RTSP). Um nó [de origem RTSP](../../../media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó [do processador de extensão HTTP.](../../../media-graph-concept.md#http-extension-processor) 

O nó de extensão HTTP desempenha o papel de um representante. Ele amostra os quadros de vídeo de entrada definidos por você usando o `samplingOptions` campo e também converte os quadros de vídeo para o tipo de imagem especificado. Em seguida, transmite a imagem sobre REST para outro módulo de borda que executa um modelo de IA atrás de um ponto final HTTP. Neste exemplo, este módulo de borda é construído utilizando o modelo [YOLOv3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos. O nó do processador de extensão HTTP recolhe os resultados de deteção e publica eventos no nó [de pia IoT Hub.](../../../media-graph-concept.md#iot-hub-message-sink) O nó envia então esses eventos para [ioT Edge Hub](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

Neste arranque rápido, você vai:

1. Criar e implementar o gráfico de mídia.
1. Interprete os resultados.
1. Limpe os recursos.
---
ms.openlocfilehash: 8d18b2e72a2a9e787f69d6adaeae8ebc3ca162b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582452"
---
![Descrição geral](../../../media/quickstarts/gRPC-extension.svg)

Este diagrama mostra como os sinais fluem neste arranque rápido. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor Real-Time de Protocolo de Streaming (RTSP). Um nó [de origem RTSP](../../../media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador de deteção de movimento.](../../../media-graph-concept.md#motion-detection-processor) Este processador detetará movimento e, após a deteção, empurrará os quadros de vídeo para o nó [do processador de extensão gRPC.](../../../media-graph-concept.md#grpc-extension-processor)

O nó de extensão gRPC desempenha o papel de um representante. Converte os quadros de vídeo para o tipo de imagem especificado. Em seguida, transmite a imagem sobre o gRPC a outro módulo de borda que executa um modelo de IA atrás de um ponto final gRPC sobre uma [memória partilhada](https://en.wikipedia.org/wiki/Shared_memory). Neste exemplo, este módulo de borda é construído utilizando o modelo [YOLOv3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos. O nó do processador de extensão gRPC recolhe os resultados de deteção e publica eventos no nó [de pia IoT Hub.](../../../media-graph-concept.md#iot-hub-message-sink) O nó envia então esses eventos para [ioT Edge Hub](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

Neste arranque rápido, você vai:

1. Criar e implementar o gráfico de mídia.
1. Interprete os resultados.
1. Limpe os recursos.
---
ms.openlocfilehash: 97c21ca300ee070b2cebaa01a585c1618899b1eb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691826"
---

![Descrição geral](../../../media/quickstarts/overview-grpc.png)

Este diagrama mostra como os sinais fluem neste arranque rápido. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor de Protocolo de Streaming em Tempo Real (RTSP). Um nó [de origem RTSP](../../../media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador de deteção de movimento.](../../../media-graph-concept.md#motion-detection-processor) Este processador detetará movimento e, após a deteção, empurrará os quadros de vídeo para o nó [do processador de extensão gRPC.](../../../media-graph-concept.md#grpc-extension-processor)

O nó de extensão gRPC desempenha o papel de um representante. Converte os quadros de vídeo para o tipo de imagem especificado. Em seguida, transmite a imagem sobre o gRPC a outro módulo de borda que executa um modelo de IA atrás de um ponto final gRPC sobre uma [memória partilhada](https://en.wikipedia.org/wiki/Shared_memory). Neste exemplo, este módulo de borda é construído utilizando o modelo [YOLOv3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos. O nó do processador de extensão gRPC recolhe os resultados de deteção e publica eventos no nó [de pia IoT Hub.](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink) O nó envia então esses eventos para [ioT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

Neste arranque rápido, você vai:

1. Criar e implementar o gráfico de mídia.
1. Interprete os resultados.
1. Limpe os recursos.

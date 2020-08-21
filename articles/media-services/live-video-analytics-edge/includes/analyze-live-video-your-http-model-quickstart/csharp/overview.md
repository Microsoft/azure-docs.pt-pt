---
ms.openlocfilehash: afe5ec179826b6d8dbef54ef773948bf9cbaea2f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684187"
---

![Descrição geral](../../../media/quickstarts/overview-qs5.png)

Este diagrama mostra como os sinais fluem neste arranque rápido. Um [módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que hospeda um servidor de Protocolo de Streaming em Tempo Real (RTSP). Um nó [de origem RTSP](../../../media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador do processador da taxa de fotogramas.](../../../media-graph-concept.md#frame-rate-filter-processor) Este processador limita a taxa de fotogramas do fluxo de vídeo que atinge o nó [do processador de extensão HTTP.](../../../media-graph-concept.md#http-extension-processor) 

O nó de extensão HTTP desempenha o papel de um representante. Converte os quadros de vídeo para o tipo de imagem especificado. Em seguida, transmite a imagem sobre REST para outro módulo de borda que executa um modelo de IA atrás de um ponto final HTTP. Neste exemplo, este módulo de borda é construído utilizando o modelo [YOLOv3,](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) que pode detetar muitos tipos de objetos. O nó do processador de extensão HTTP recolhe os resultados de deteção e publica eventos no nó [de pia IoT Hub.](../../../media-graph-concept.md#iot-hub-message-sink) O nó envia então esses eventos para [ioT Edge Hub](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Neste arranque rápido, você vai:

1. Criar e implementar o gráfico de mídia.
1. Interprete os resultados.
1. Limpe os recursos.

---
ms.openlocfilehash: 9c1b521a0f10da77295fd2457793566d787cb2cd
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570017"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs4.svg" alt-text="Fluxo de sinais":::

O diagrama anterior mostra como os sinais fluem neste arranque rápido. [Um módulo de borda](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula uma câmara IP que acolhe um servidor de Protocolo de Streaming em Tempo Real (RTSP). Um nó [de origem RTSP](../../../media-graph-concept.md#rtsp-source) puxa o feed de vídeo deste servidor e envia quadros de vídeo para o nó do [processador de deteção de movimento.](../../../media-graph-concept.md#motion-detection-processor) A fonte RTSP envia os mesmos quadros de vídeo para um nó [do processador do portão de sinal,](../../../media-graph-concept.md#signal-gate-processor) que permanece fechado até ser desencadeado por um evento.

Quando o processador de deteção de movimento deteta movimento no vídeo, envia um evento para o nó do processador do portão de sinal, desencadeando-o. O portão abre-se durante a duração configurada do tempo, enviando molduras de vídeo para o nó da pia do [ficheiro.](../../../media-graph-concept.md#file-sink) Este nó da pia grava o vídeo como um ficheiro MP4 no sistema de ficheiros local do seu dispositivo de borda. O ficheiro é guardado no local configurado.

Neste arranque rápido, você vai:

1. Criar e implementar o gráfico de mídia.
1. Interprete os resultados.
1. Limpe os recursos.

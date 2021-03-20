---
title: Gravação de vídeo baseada em eventos - Azure
description: A gravação de vídeo baseada em eventos (EVR) refere-se ao processo de gravação de vídeo desencadeado por um evento. O evento em questão pode ter origem devido ao processamento do próprio sinal de vídeo (por exemplo, deteção em movimento) ou pode ser de uma fonte independente (por exemplo, abertura de uma porta).  Alguns casos de utilização relacionados com a gravação de vídeo baseada em eventos são descritos neste artigo.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 6a5f4873b2cfef8d9a6594916d82cd30a3bc1cc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97401619"
---
# <a name="event-based-video-recording"></a>Gravação de vídeo baseada em eventos  
 
## <a name="suggested-pre-reading"></a>Pré-leitura sugerida  

* [Gravação de vídeo contínua](continuous-video-recording-concept.md)
* [Reprodução de conteúdo gravado](video-playback-concept.md)
* [Conceito de gráfico de mídia](media-graph-concept.md)

## <a name="overview"></a>Descrição geral 

A gravação de vídeo baseada em eventos (EVR) refere-se ao processo de gravação de vídeo desencadeado por um evento. O evento em questão, pode ter origem devido ao processamento do próprio sinal de vídeo (por exemplo, deteção em movimento) ou pode ser de uma fonte independente (por exemplo, abertura de uma porta). 

Pode gravar vídeo (desencadeado por um evento) de uma câmara CCTV para um ativo dos Media Services utilizando um gráfico de mídia constituído por um nó de [origem RTSP,](media-graph-concept.md#rtsp-source) um nó [de pia de ativo](media-graph-concept.md#asset-sink) e outros nós, tal como descritos nos casos de utilização abaixo. Pode configurar o nó [da pia](media-graph-concept.md#asset-sink) do ativo para gerar novos ativos cada vez que ocorre um evento, de modo que o vídeo que corresponde a cada evento será no seu próprio ativo. Também pode optar por usar um ativo para guardar o vídeo para todos os eventos. 

Como alternativa ao nó da pia do ativo, pode utilizar um nó [de lavatório de ficheiros](media-graph-concept.md#file-sink) para capturar pequenos fragmentos de vídeo (relacionados com um evento de interesse), para uma localização especificada no sistema de ficheiros local no dispositivo Edge. 

Alguns casos de utilização relacionados com a gravação de vídeo baseada em eventos são descritos neste artigo.

### <a name="video-recording-based-on-motion-detection"></a>Gravação de vídeo baseada na deteção de movimentos  

Neste caso de utilização, só pode gravar videoclips quando houver movimento detetado no vídeo e ser alertado quando tal videoclip é gerado. Isto poderia ser relevante em cenários de segurança comercial que envolvam a proteção de infraestruturas críticas. Ao gerar alertas e gravar vídeos quando é detetado um movimento inesperado, pode melhorar a eficiência do operador humano, uma vez que a ação só é necessária quando o alerta é gerado.

O diagrama abaixo mostra uma representação gráfica de um gráfico mediático que aborda esta caixa de utilização. A representação JSON da topologia de gráficos de tal gráfico mediático pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Gravação de vídeo baseada na deteção de movimentos":::

No diagrama, o nó de origem RTSP captura o feed de vídeo ao vivo da câmara e entrega-o a um nó do [processador de deteção de movimento.](media-graph-concept.md#motion-detection-processor) Ao detetar movimento no vídeo ao vivo, o nó do processador de deteção de movimento gera evento que vai para o nó do [processador do portão de sinal,](media-graph-concept.md#signal-gate-processor) bem como para o nó de pia de mensagem IoT Hub. Este último nó envia os eventos para o IoT Edge Hub, de onde podem ser encaminhados para outros destinos para desencadear alertas. 

Um evento do nó do detetor de movimento irá acionar o nó do processador do portão de sinal, e permitirá que o vídeo em direto do nó de origem RTSP flua para o nó da pia do ativo. Na ausência de eventos subsequentes de deteção de movimentos, o portão fechar-se-á após um período de tempo configurado. Isto determina a duração do vídeo gravado.

### <a name="video-recording-based-on-events-from-other-sources"></a>Gravação de vídeo baseada em eventos de outras fontes  

Neste caso de utilização, podem ser utilizados sinais de outro sensor IoT para ativar a gravação de vídeo. O diagrama abaixo mostra uma representação gráfica de um gráfico mediático que aborda esta caixa de utilização. A representação JSON da topologia de gráficos de tal gráfico mediático pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Gravação de vídeo baseada em eventos de outras fontes":::

No diagrama, o sensor externo envia eventos para o IoT Edge Hub. Os eventos são então encaminhados para o nó do processador do portão de sinal através do nó [de mensagem IoT Hub.](media-graph-concept.md#iot-hub-message-source) O comportamento do nó do processador do portão de sinal é o mesmo que no caso de utilização anterior - abrirá e deixará o feed de vídeo ao vivo fluir do nó de origem RTSP para o nó da pia do ficheiro (ou nó do lavatório do ativo) quando é acionado pelo evento externo. 

Se utilizar um nó de lavatório de ficheiros, o vídeo será gravado no sistema de ficheiros local no dispositivo de borda. Caso contrário, se utilizar um nó de pia de ativo, o vídeo será gravado num ativo.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Gravação de vídeo baseada num módulo de inferenculação externo 

Neste caso de utilização, pode gravar videoclips com base num sinal de um sistema lógico externo. Um exemplo de tal caso de utilização só pode ser gravar um videoclip quando um camião é detetado no vídeo do trânsito numa autoestrada. O diagrama abaixo mostra uma representação gráfica de um gráfico mediático que aborda esta caixa de utilização. A representação JSON da topologia de gráficos de tal gráfico mediático pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Gravação de vídeo baseada num módulo de inferenculação externo":::

No diagrama, o nó de origem RTSP capta o feed de vídeo ao vivo da câmara e entrega-o a dois ramos: um tem um nó [do processador do portão](media-graph-concept.md#signal-gate-processor) de sinal e o outro usa um nó de [extensão HTTP](media-graph-concept.md) para enviar dados para um módulo de lógica externa. O nó de extensão HTTP permite que o gráfico de mídia envie quadros de imagem (em formato JPEG, BMP ou PNG) para um serviço de inferência externa sobre REST. Este caminho de sinal normalmente só suporta taxas de fotogramas baixas (<5fps). Pode utilizar o nó do processador de extensão HTTP para baixar a taxa de fotogramas do vídeo que vai para o módulo de inferencing externo.

Os resultados do serviço de inferência externa são recuperados pelo nó de extensão HTTP e transmitidos ao hub IoT Edge através do nó de pia de mensagem IoT Hub, onde podem ser processados pelo módulo de lógica externa. Se o serviço de inferência for capaz de detetar veículos, por exemplo, o módulo lógico pode procurar um veículo específico, como um autocarro ou um camião. Quando o módulo lógico deteta o objeto de interesse, pode acionar o nó do processador do portão de sinal enviando um evento através do IoT Edge Hub para o nó de origem da mensagem IoT Hub no gráfico. A saída do portão de sinal é mostrada para ir para um nó de lavatório de arquivo ou um nó de pia de ativo. No primeiro caso, o vídeo será gravado no sistema de ficheiros local no dispositivo de borda. Neste último caso, o vídeo será gravado para um ativo.

Um melhoramento a este exemplo é utilizar um processador de detetor de movimento antes do nó do processador de extensão HTTP. Isto reduzirá a carga no serviço de inferência, como durante a noite, quando pode haver longos períodos de tempo quando não há veículos na autoestrada. 

## <a name="next-steps"></a>Passos seguintes

[Tutorial: gravação de vídeo baseada em eventos](event-based-video-recording-tutorial.md)

---
title: Analisando vídeo ao vivo sem qualquer gravação - Azure
description: Um gráfico de mídia pode ser usado apenas para extrair análises de um stream de vídeo ao vivo, sem ter que registrá-lo na borda ou na nuvem. Este artigo discute este conceito.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 25a7cadc47603b726542fa391d441e1fbca78908
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97398979"
---
# <a name="analyzing-live-video-without-any-recording"></a>Analisar vídeo ao vivo sem qualquer gravação

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida 

* [Conceito de gráfico de mídia](media-graph-concept.md)
* [Gravação de vídeo baseada em eventos](event-based-video-recording-concept.md)

## <a name="overview"></a>Descrição Geral  

Pode utilizar um gráfico de mídia para analisar o vídeo ao vivo, sem registar partes do vídeo num ficheiro ou num ativo. Os gráficos mediáticos apresentados abaixo são semelhantes aos do artigo sobre [Gravação de Vídeo Baseado](event-based-video-recording-concept.md)em Eventos, mas sem um nó de lavatório ou nó de lavatório de ficheiros.

### <a name="motion-detection"></a>Deteção de movimentos

O gráfico de mídia mostrado abaixo é composto por um nó de [origem RTSP,](media-graph-concept.md#rtsp-source) um nó do [processador de deteção de movimento](media-graph-concept.md#motion-detection-processor) e um nó de pia de [mensagem IoT Hub.](media-graph-concept.md#iot-hub-message-sink) A representação JSON da topologia de gráficos de tal gráfico mediático pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json). Este gráfico permite-lhe detetar movimento na transmissão de vídeo ao vivo e transmitir os eventos de movimento para outras aplicações e serviços através do nó de pia de mensagem IoT Hub. As aplicações ou serviços externos podem desencadear um alerta ou enviar uma notificação ao pessoal apropriado.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Análise de vídeo ao vivo baseada na deteção de movimentos":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>Analisar vídeo usando um modelo de visão personalizado 

O gráfico de mídia mostrado abaixo permite-lhe analisar um fluxo de vídeo ao vivo usando um modelo de visão personalizado embalado num módulo separado. A representação JSON da topologia de gráficos de tal gráfico mediático pode ser encontrada [aqui](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json). Pode ver alguns exemplos [aqui](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) sobre embrulhar modelos em módulos IoT Edge que funcionam como um serviço de inferência.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="Análise de vídeo ao vivo baseada num módulo de inferencção externo":::

Neste gráfico de mídia, a entrada de vídeo da fonte RTSP é enviada para um nó [de processador de extensão HTTP,](media-graph-concept.md#http-extension-processor) que envia quadros de imagem (em formato JPEG, BMP ou PNG) para um serviço de inferência externa sobre REST. Os resultados do serviço de inferência externa são recuperados pelo nó de extensão HTTP e transmitidos ao hub IoT Edge através do nó de pia da mensagem IoT Hub. Este tipo de gráfico de mídia pode ser usado para construir soluções para uma variedade de cenários, tais como compreender a distribuição em série de tempo de veículos em um cruzamento, compreender o padrão de tráfego do consumidor em uma loja de retalho, e assim por diante.
>[!TIP]
> Pode gerir a taxa de fotogramas dentro do nó do processador de extensão HTTP utilizando o `samplingOptions` campo antes de enviá-lo a jusante.

Um melhoramento a este exemplo é utilizar um processador de detetor de movimento antes do nó do processador de extensão HTTP. Isto reduzirá a carga no serviço de inferência, uma vez que é utilizado apenas quando há atividade de movimento no vídeo.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="Análise de vídeo ao vivo com base em quadros detetados por movimento através de módulo de inferenculação externo":::

## <a name="next-steps"></a>Passos seguintes

[Gravação de vídeo contínua](continuous-video-recording-concept.md)

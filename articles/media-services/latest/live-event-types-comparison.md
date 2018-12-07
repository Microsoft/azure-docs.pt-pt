---
title: Tipos de LiveEvent de serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo mostra uma tabela detalhada que comparam LiveEvent tipos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/27/2018
ms.author: juliako
ms.openlocfilehash: 2dbc2b4e316155e880d3baa900196d5cdd31be72
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011781"
---
# <a name="liveevent-types-comparison"></a>Comparação de tipos de LiveEvent

Nos serviços de multimédia do Azure, um [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação em tempo real e pass-through. 

## <a name="types-comparison"></a>Comparação de tipos 

A tabela seguinte compara as funcionalidades dos dois tipos de LiveEvent.

| Funcionalidade | LiveEvent pass-through | Padrão LiveEvent (básico) |
| --- | --- | --- |
| Velocidade de transmissão única entrada é codificada em múltiplas velocidades de transmissão na cloud |Não |Sim |
| Resolução máxima de vídeo para feed de contribuição |4K (4096 x 2160 em 60 quadros por segundo) |1080p (1920 x 1088 em 30 quadros por segundo)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Máximas camadas de dados de saída| Mesmo que a entrada|Até 7|
| Feed de largura de banda agregada máxima de contribuição|60 Mbps|N/A|
| Velocidade de transmissão máxima para uma única camada na contribuição |20 Mbps|20 Mbps|
| Suporte para faixas de áudio de vários idiomas|Sim|Não|
| Codecs de vídeo de entrada suportado |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída suportados|Mesmo que a entrada|H.264/AVC|
| Suportado vídeo bitová hloubka, entrada e saída|Até 10 bits incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada suportadas|AAC-LC, HE-AAC v1, v2 HE-AAC|AAC-LC, HE-AAC v1, v2 HE-AAC|
| Codecs de áudio de saída suportados|Mesmo que a entrada|AAC-LC|
| Resolução máxima de vídeo de vídeo de saída|Mesmo que a entrada|720p (em 30 quadros por segundo)|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Preço|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto"|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto"|
| Tempo de execução máximo|24 x 365 live linear|24x7|
| Capacidade de transmitir através de embedded 608 de legenda oculta CEA/708 legendagem de áudio de dados|Sim|Sim|
| Suporte para a inserção de slates|Não|Não|
| Suporte para ad sinalização através da API| Não|Não|
| Suporte para ad sinalização por meio de mensagens do SCTE 35 em banda|Sim|Sim|
| Capacidade de recuperar de paralisações breves no feed de contribuição|Sim|Não (LiveEvent começará slating após 6 + segundos sem dados de entrada)|
| Suporte para não-uniforme GOPs de entrada|Sim|Não – entrada deve ter resolvido duração GOP|
| Suporte para entrada de taxa de quadros de variável|Sim|Não – entrada deve ser corrigida taxa de quadros. Secundárias variações são pela tolerar, por exemplo, durante o plano de movimento elevada. Mas o feed de contribuição não é possível remover a taxa de quadros (por exemplo, para 15 quadros por segundo).|
| Auto-shutoff de LiveEvent quando a introdução do feed é perdido|Não|Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)

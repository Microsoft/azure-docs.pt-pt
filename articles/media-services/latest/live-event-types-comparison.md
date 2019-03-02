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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243895"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de evento em direto

Nos serviços de multimédia do Azure, um [evento em direto](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação em tempo real e pass-through. 

## <a name="types-comparison"></a>Comparação de tipos 

A tabela seguinte compara as funcionalidades dos dois tipos de evento em direto.

| Funcionalidade | Evento em direto de pass-through | Padrão de eventos em direto |
| --- | --- | --- |
| Velocidade de transmissão única entrada é codificada em múltiplas velocidades de transmissão na cloud |Não |Sim |
| Resolução máxima de vídeo para feed de contribuição |4K (4096 x 2160 em 60 quadros por segundo) |1080p (1920 x 1088 em 30 quadros por segundo)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Máximas camadas de dados de saída| Mesmo que a entrada|Até 6 (consulte as predefinições do sistema abaixo)|
| Feed de largura de banda agregada máxima de contribuição|60 Mbps|N/A|
| Velocidade de transmissão máxima para uma única camada na contribuição |20 Mbps|20 Mbps|
| Suporte para faixas de áudio de vários idiomas|Sim|Não|
| Codecs de vídeo de entrada suportado |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída suportados|Mesmo que a entrada|H.264/AVC|
| Suportado vídeo bitová hloubka, entrada e saída|Até 10 bits incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada suportadas|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída suportados|Mesmo que a entrada|AAC-LC|
| Resolução máxima de vídeo de vídeo de saída|Mesmo que a entrada|720p (em 30 quadros por segundo)|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Preço|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto"|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto"|
| Tempo de execução máximo| 24 horas x 365 dias, em direto lineares | Até 24 horas|
| Capacidade de transmitir através de embedded 608 de legenda oculta CEA/708 legendagem de áudio de dados|Sim|Sim|
| Suporte para a inserção de slates|Não|Não|
| Suporte para ad sinalização através da API| Não|Não|
| Suporte para ad sinalização por meio de mensagens do SCTE 35 em banda|Sim|Sim|
| Capacidade de recuperar de paralisações breves no feed de contribuição|Sim|Não (evento em direto começará slating após 6 + segundos sem dados de entrada)|
| Suporte para não-uniforme GOPs de entrada|Sim|Não – entrada deve ter resolvido duração GOP|
| Suporte para entrada de taxa de quadros de variável|Sim|Não – entrada deve ser corrigida taxa de quadros. Secundárias variações são pela tolerar, por exemplo, durante o plano de movimento elevada. Mas o feed de contribuição não é possível remover a taxa de quadros (por exemplo, para 15 quadros por segundo).|
| Auto-shutoff de evento em direto quando a introdução do feed é perdido|Não|Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="system-presets"></a>Predefinições do sistema

Ao usar a codificação em tempo real (evento em direto é definido como **padrão**), a predefinição de codificação define como o fluxo de entrada é codificado em várias velocidades de transmissão ou camadas. Atualmente, o único valor permitido para a configuração predefinida é *Default720p* (predefinição).

**Default720p** irá codificar o vídeo para as camadas de 6 seguintes.

### <a name="output-video-stream"></a>Stream de vídeo de saída

| BitRate | Largura | Altura | MaxFPS | Perfil | Nome do Stream de saída |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Elevado |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Elevado |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Elevado |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Elevado |Video_512x288_850kbps |
| 550 |384 |216 |30 |Elevado |Video_384x216_550kbps |
| 200 |340 |192 |30 |Elevado |Video_340x192_200kbps |

> [!NOTE]
> Se precisar de utilizar uma predefinição de codificação em direto personalizada, entre em contato com amshelp@microsoft.com. Deve especificar a tabela desejada de resolução e velocidades de transmissão. Certifique-se de que existe apenas uma camada em 720p e no máximo, 6 camadas.

### <a name="output-audio-stream"></a>Stream de áudio de saída

Áudio está codificado para estéreo AAC-LC em 128 kbps, taxa de amostragem de 48 kHz.

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)

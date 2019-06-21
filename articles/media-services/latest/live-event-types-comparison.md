---
title: Tipos de LiveEvent de serviços de multimédia do Azure | Documentos da Microsoft
description: Este artigo mostra uma tabela detalhada que compara os tipos de LiveEvent.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150371"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de evento em direto

Nos serviços de multimédia do Azure, um [evento em direto](https://docs.microsoft.com/rest/api/media/liveevents) pode ser um dos dois tipos: codificação em tempo real e pass-through. 

## <a name="types-comparison"></a>Comparação de tipos 

A tabela seguinte compara as funcionalidades dos tipos de evento em direto. Os tipos são definidos durante a criação usando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -um codificador em direto no local envia um fluxo de velocidade de transmissão múltiplas. As transmissões em fluxo passa por meio do evento em direto sem qualquer processamento adicional. 
* **LiveEventEncodingType.Standard** – um codificador em direto envia um fluxo de velocidade de transmissão única para o evento em direto e os serviços de multimédia cria vários fluxos de velocidade de transmissão no local. Se o feed de contribuição é da 720p ou resolução superior, o **Default720p** configuração predefinida será codificar um conjunto de pares de velocidade de transmissão/resolução 6 (siga detalhes posteriormente neste artigo).
* **LiveEventEncodingType.Premium1080p** – um codificador em direto envia um fluxo de velocidade de transmissão única para o evento em direto e os serviços de multimédia cria vários fluxos de velocidade de transmissão no local. A configuração predefinida de Default1080p Especifica o conjunto de saída de pares de resolução/velocidade de transmissão (siga detalhes posteriormente neste artigo). 

| Funcionalidade | Evento em direto de pass-through | Padrão ou Premium1080p de eventos em direto |
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
| Resolução máxima de vídeo de vídeo de saída|Mesmo que a entrada|Standard - 720p, Premium1080p - 1080p|
| Taxa máxima de quadros de vídeo de entrada|60 quadros por segundo|Padrão ou Premium1080p - 30 quadros por segundo|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Preço|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto"|Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto"|
| Tempo de execução máximo| 24 horas x 365 dias, em direto lineares | Até 24 horas|
| Capacidade de transmitir através de embedded 608 de legenda oculta CEA/708 legendagem de áudio de dados|Sim|Sim|
| Suporte para a inserção de slates|Não|Não|
| Suporte para ad sinalização através da API| Não|Não|
| Suporte para ad sinalização por meio de mensagens do SCTE 35 em banda|Sim|Sim|
| Capacidade de recuperar de paralisações breves no feed de contribuição|Sim|Parcial|
| Suporte para não-uniforme GOPs de entrada|Sim|Não – entrada deve ter resolvido duração GOP|
| Suporte para entrada de taxa de quadros de variável|Sim|Não – entrada deve ser corrigida taxa de quadros. Secundárias variações são pela tolerar, por exemplo, durante o plano de movimento elevada. Mas o feed de contribuição não é possível remover a taxa de quadros (por exemplo, para 15 quadros por segundo).|
| Auto-shutoff de evento em direto quando a introdução do feed é perdido|Não|Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="system-presets"></a>Predefinições do sistema

As resoluções e velocidades de transmissão contidas na saída do codificador em direto são determinadas pela [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se utilizar um **padrão** live encoder (LiveEventEncodingType.Standard), em seguida, o *Default720p* configuração predefinida especifica um conjunto de pares de velocidade de transmissão/resolução 6 descrito abaixo. Caso contrário, se utilizar um **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), em seguida, o *Default1080p* configuração predefinida especifica o conjunto de saída de pares de resolução/velocidade de transmissão.

> [!NOTE]
> Não é possível aplicar o Default1080p configuração predefinida para um evento em direto se foi a configuração padrão de codificação em direto, obterá um erro. Também receberá um erro se tentar aplicar o Default720p configuração predefinida para um codificador em direto Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Fluxos de vídeo de saída para Default720p

Se o feed de contribuição é da 720p ou resolução superior, o **Default720p** configuração predefinida será codificar transmissão em camadas de 6 a seguir. Na tabela abaixo, velocidade de transmissão é em kbps, MaxFPS representa a taxa de quadros permitido máximo (em quadros por segundo), perfil representa o perfil de H.264 utilizado.

| Velocidade de transmissão | Largura | Altura | MaxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alta |
| 2200 |960 |540 |30 |Alta |
| 1350 |704 |396 |30 |Alta |
| 850 |512 |288 |30 |Alta |
| 550 |384 |216 |30 |Alta |
| 200 |340 |192 |30 |Alta |

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação em direto, abra um pedido de suporte através do Portal do Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Confirme que há apenas uma camada a 720p e seis camadas no máximo. Também especifica que está a solicitar uma predefinição de um codificador em direto Standard.
> Os valores específicos das resoluções e velocidades de transmissão podem ser ajustados ao longo do tempo

### <a name="output-video-streams-for-default1080p"></a>Fluxos de vídeo de saída para Default1080p

Se o feed de contribuição é da resolução de 1080p, o **Default1080p** configuração predefinida será codificar transmissão em camadas de 6 a seguir.

| Velocidade de transmissão | Largura | Altura | MaxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alta |
| 3000 |1280 |720 |30 |Alta |
| 1600 |960 |540 |30 |Alta |
| 800 |640 |360 |30 |Alta |
| 400 |480 |270 |30 |Alta |
| 200 |320 |180 |30 |Alta |

> [!NOTE]
> Se precisar de personalizar a predefinição de codificação em direto, abra um pedido de suporte através do Portal do Azure. Deve especificar a tabela de resolução e velocidades de transmissão pretendida. Certifique-se de que existe apenas uma camada em 1080p e no máximo, 6 camadas. Também especifica que está a solicitar uma predefinição de um codificador em direto de Premium1080p.
> Os valores específicos das resoluções e velocidades de transmissão podem ser ajustados ao longo do tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Stream de áudio de saída para Default720p e Default1080p

Para ambos *Default720p* e *Default1080p* configurações predefinidas, de áudio está codificado para estéreo AAC-LC em 128 kbps. A frequência de amostragem segue-se que a faixa de áudio a contribuição do feed.

## <a name="implicit-properties-of-the-live-encoder"></a>Propriedades implícitas de codificador em direto

A seção anterior descreve as propriedades do codificador em direto que podem ser controlados explicitamente, por meio da configuração predefinida – como o número de camadas, resoluções e velocidades de transmissão. Esta secção esclarece as propriedades implícitas.

### <a name="group-of-pictures-gop-duration"></a>Grupo de duração de imagens (GOP)

O codificador em direto segue a [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) estrutura da contribuição feed - o que significa que as camadas de saída terá a mesma duração GOP. Por este motivo, recomenda-se que configure o codificador no local para produzir uma feed de contribuição corrigiu duração de GOP (normalmente, 2 segundos). Isto irá garantir que os fluxos de HLS e MPEG DASH saídos do serviço também corrigiu durações GOP. Pequenas variações nos durações GOP provável pela tolerar a maioria dos dispositivos.

### <a name="frame-rate"></a>Taxa de quadros

O codificador em direto também segue as durações de quadros individuais de vídeo na contribuição feed - o que significa que as camadas de saída terá quadros com as durações do mesmo. Por este motivo, recomenda-se que configure o codificador no local para produzir uma contribuição feed que corrigiu a taxa de quadros (no máximo 30 quadros por segundo). Isto irá garantir que os fluxos de HLS e MPEG DASH saídos do serviço também corrigiu durações de taxas de quadros. Pequenas variações em taxas de quadros pela podem tolerar pela maioria dos dispositivos, mas não é garantido que o codificador em direto produzirá um resultado que reproduzirá corretamente. Seu codificador em direto no local deve não ser remover quadros (ex. em condições de pouca bateria) ou variados a taxa de quadros de qualquer forma.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Resolução de contribuição de feed e camadas de saída

O codificador em direto é configurado para evitar upconverting a contribuição do feed. Como resultado a resolução máxima de camadas de saída não exceder que a contribuição do feed.

Por exemplo, se tiver de enviar uma contribuição feed de cada 720p para codificação em direto de um evento em direto configurado para Default1080p, a saída terá apenas 5 camadas, começando com 720p em 3 Mbps, vai até 1080p em 200 kbps. Ou, se tiver de enviar uma contribuição feed de cada 360p num evento em direto configurado para padrão de codificação em direto, a saída irá conter 3 camadas (em resoluções de 288p, 216p e p de 192). No caso de degeneração, se tiver de enviar um feed de contribuição de, digamos, pixels de 160 x 90 para um codificador em direto Standard, o resultado irá conter uma camada a resolução de 160 x 90, a velocidade de transmissão mesmo que a contribuição do feed.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Velocidade de transmissão de contribuição de feed e camadas de saída

O codificador em direto é configurado que respeite as definições de velocidade de transmissão na configuração predefinida, independentemente da velocidade de transmissão da contribuição feed. Como resultado a velocidade de transmissão das camadas de saída pode exceder os que a contribuição do feed. Por exemplo, se enviar numa contribuição feed de cada uma resolução de 720p em 1 Mbps, as camadas de saída irão permanecer a mesma que no [tabela](live-event-types-comparison.md#output-video-streams-for-default720p) acima.

## <a name="next-steps"></a>Passos Seguintes

[Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)

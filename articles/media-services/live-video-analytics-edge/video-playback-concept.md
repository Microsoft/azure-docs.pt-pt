---
title: Reprodução de vídeo - Azure
description: Marcador de posição
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: be42b39c167798e4e288c77dbd48a8734eb71fdf
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278597"
---
# <a name="video-playback"></a>Reproduzir vídeo 

## <a name="suggested-pre-reading"></a>Pré-leitura sugerida 

* [Análise de vídeo ao vivo na visão geral do IoT Edge](overview.md)
* [Análise de vídeo ao vivo na terminologia IoT Edge](terminology.md)
* [Conceito de gráfico de mídia](media-graph-concept.md)

## <a name="overview"></a>Descrição Geral  

Pode utilizar [gráficos de mídia](media-graph-concept.md) para gravar vídeo num [ativo](terminology.md#asset)da Azure Media Services . Neste documento, pode aprender sobre os passos que precisa de tomar para jogar um ativo utilizando as capacidades de streaming existentes da Azure Media Services.

## <a name="streaming-endpoint"></a>Ponto final de transmissão em fluxo 

Pode utilizar o Azure Media Services para [transmitir](terminology.md#streaming) o ativo a jogadores de vídeo utilizando protocolos de streaming de mídia baseados em HTTP, padrão da indústria, como HTTP Live Streaming (HLS) e MPEG-DASH. Esta conversão de meios de comunicação a partir de conteúdos gravados em formatos de streaming é gerida por um [ponto final de streaming](../latest/stream-streaming-endpoint-concept.md), que é um recurso que precisa de prestar na sua conta do Azure Media Service.

## <a name="streaming-policy"></a>Política de streaming 

A Azure Media Services oferece-lhe diferentes métodos para proteger os seus streams de vídeo, como discutido em Proteger o seu conteúdo com o artigo [de encriptação dinâmica dos Media Services.](../latest/drm-content-protection-concept.md) A um nível elevado, as opções de proteção de conteúdos são:

* **Streaming in-the-clear** - onde nenhuma encriptação é aplicada durante o streaming.
* **Utilize o Advanced Encryption Standard (AES-128)** – e implemente um método para entregar as teclas para desencriptar o vídeo apenas para os espectadores autenticados.
* **Utilize sistemas de Gestão de Direitos Digitais (DRM)** – para controlar a utilização, modificação e entrega de vídeo a dispositivos que aplicam estas políticas.

Para obter a proteção de conteúdos, pode definir e criar uma [Política de Streaming](../latest/stream-streaming-policy-concept.md) na sua conta de Media Service, e usá-la para transmitir todos os ativos (assumindo que todos os fluxos têm os mesmos requisitos de segurança). Também pode utilizar qualquer uma das políticas predefinidas (como Predefined_ClearStreamingOnly).

## <a name="streaming-locator"></a>Localizador de streaming  

Uma vez iniciado um Streaming Endpoint na sua conta de Media Service, e a política de streaming definida, pode proceder à transmissão de meios de comunicação registados a partir de um ativo através de protocolos HLS ou DASH. Os web-players e as aplicações móveis precisam de um URL que indique o fluxo HLS ou DASH. Pode construir este URL utilizando o [localizador de streaming.](../latest/stream-streaming-locators-concept.md) Como discutido nesse artigo, e mostrado na [Criar um localizador de streaming e construir amostra de URLs,](../latest/create-streaming-locator-build-url.md) o URL de streaming é composto a partir do ponto final de streaming, política de streaming e do localizador de streaming.

## <a name="content-recorded-using-file-sink"></a>Conteúdo gravado usando pia de ficheiro  

Como descrito na [pia de ficheiros de gráficos de mídia,](media-graph-concept.md#file-sink)pode utilizar gráficos de mídia para gravar vídeos no sistema de ficheiros local do dispositivo de borda usando uma pia de ficheiro no seu gráfico de mídia. O lavatório de ficheiros gera ficheiros [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) e pode utilizar o elemento [ &lt; de vídeo &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) HTML5 para reproduzir tal conteúdo. 

## <a name="next-steps"></a>Passos seguintes

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->

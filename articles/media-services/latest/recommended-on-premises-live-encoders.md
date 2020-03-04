---
title: Codificadores de streaming ao vivo recomendados pela Media Services - Azure Microsoft Docs
description: Saiba mais sobre os codificadores de streaming ao vivo no local recomendados pelos Media Services
services: media-services
keywords: codificação;codificadores;meios
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 43d2a56757948f6005420d2b44f9bea963613d17
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252181"
---
# <a name="recommended-live-streaming-encoders"></a>Codificadores de transmissão em direto recomendados

No Azure Media Services, um [Live Event](https://docs.microsoft.com/rest/api/media/liveevents) (canal) representa um pipeline para o processamento de conteúdos em streaming. O Live Event recebe streams de entrada ao vivo de uma de duas maneiras.

* Um codificador ao vivo no local envia um fluxo de RTMP ou Smooth Streaming (MP4 fragmentado) para o Live Event que não está habilitado a realizar codificação ao vivo com os Media Services. Os streams ingeridos passam por Eventos Ao Vivo sem qualquer processamento adicional. Este método **chama-se pass-through**. Recomendamos que o codificador ao vivo envie fluxos multibitantes em vez de um fluxo de bitrate único para um evento ao vivo pass-through para permitir o streaming de bitrate adaptativo ao cliente. 

    Se estiver a usar fluxos multibitrates para o evento ao vivo, o tamanho do VÍDEO GOP e os fragmentos de vídeo em bitrates diferentes devem ser sincronizados para evitar comportamentos inesperados no lado da reprodução.

  > [!NOTE]
  > Usar um método de passagem é a forma mais económica de fazer streaming ao vivo.
 
* Um codificador ao vivo no local envia um fluxo de bitrate único para o Live Event que está habilitado a realizar codificação ao vivo com os Media Services num dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O Live Event realiza então a codificação ao vivo do fluxo de bitrate único para um fluxo de vídeo multibitável (adaptativo).

Para obter informações detalhadas sobre a codificação ao vivo com os Media Services, consulte [o streaming em direto com os Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Requisitos codificadores

Os codificadores devem suportar o TLS 1.2 quando utilizarem protocolos HTTPS ou RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Codificadores ao vivo que output RTMP

Os Serviços de Multimédia recomendam utilizar um dos codificadores em direto seguintes que têm RTMP como saída. Os regimes de URL suportados são `rtmp://` ou `rtmps://`.

Ao transmitir em fluxo através de RTMP, verifique as definições da firewall e/ou do proxy, para confirmar que as portas TCP de saída 1935 e 1936 estão abertas.<br/><br/>
Ao transmitir em fluxo através de RTMPS, verifique as definições da firewall e/ou do proxy, para confirmar que as portas TCP de saída 2935 e 2936 estão abertas.

> [!NOTE]
> Os codificadores devem suportar o TLS 1.2 ao utilizarem protocolos RTMPS.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Ao Vivo 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (versão 2.14.15 e superior)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (versão 13.0.2 ou superior devido ao requisito TLS 1.2)
- Telestream Wirecast S (apenas o RTMP é suportado)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [Estação Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Herói 7 e Herói 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificadores ao vivo que produzem MP4 fragmentado

A Media Services recomenda a utilização de um dos seguintes codificadores ao vivo que tenham o Streaming Liso multibitado (MP4 fragmentado) como saída. Os regimes de URL suportados são `http://` ou `https://`.

> [!NOTE]
> Os codificadores devem suportar o TLS 1.2 ao utilizarem protocolos HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (versão 2.14.15 e superior devido ao requisito TLS 1.2)
- Envivio 4Caster C4 Gen III 
- Imagine comunicações Selenio MCP3
- Media Excel Hero Live and Hero 4K (UHD/HEVC)
- [Estação Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Se estiver a transmitir eventos ao vivo em vários idiomas (por exemplo, uma faixa de áudio inglesa e uma faixa de áudio espanhola), pode fazê-lo com o codificador ao vivo do Media Excel configurado para enviar o feed ao vivo para um evento ao vivo pass-through.

## <a name="configuring-on-premises-live-encoder-settings"></a>Configurar no local definições codificadoras ao vivo

Para obter informações sobre quais as configurações válidas para o seu tipo de evento ao vivo, consulte a [comparação](live-event-types-comparison.md)de tipos de eventos ao vivo .

### <a name="playback-requirements"></a>Requisitos de reprodução

Para reproduzir conteúdo, um fluxo de áudio e vídeo deve estar presente. A reprodução do fluxo só de vídeo não é suportada.

### <a name="configuration-tips"></a>Dicas de configuração

- Sempre que possível, utilize uma ligação de internet conectada.
- Quando estiver a determinar os requisitos de largura de banda, duplique os bitrates de streaming. Embora não seja obrigatória, esta regra simples ajuda a mitigar o impacto do congestionamento da rede.
- Quando utilizar codificadores baseada em software, feche todos os programas desnecessários.
- Alterar a configuração do codificador depois de ter começado a empurrar tem efeitos negativos no evento. As alterações de configuração podem fazer com que o evento se torne instável. 
- Certifique-se de que dá tempo suficiente para configurar o seu evento. Para eventos de alta escala, recomendamos iniciar a configuração uma hora antes do seu evento.

## <a name="see-also"></a>Consulte também

[Torne-se um parceiro codificador no local](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>Passos seguintes

[Streaming em direto com Media Services v3](live-streaming-overview.md)

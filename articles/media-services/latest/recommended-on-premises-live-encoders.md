---
title: Codificadores de streaming ao vivo recomendados pelos Media Services
description: Saiba mais sobre transmissão em direto nos locais recomendados pelos Media Services
services: media-services
keywords: codificação;codificadores;meios de comunicação
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: bf282b3fbba0f22cd41c0420014c46ddb7c958eb
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955144"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Codificadores de streaming verificados no local

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

No Azure Media Services, um [Live Event](/rest/api/media/liveevents) (canal) representa um oleoduto para o processamento de conteúdos de streaming em direto. O Live Event recebe streams de entrada ao vivo de uma de duas maneiras.

* Um codificador ao vivo no local envia um stream rtmp multi-bitrate ou smooth streaming (MP4 fragmentado) para o Live Event que não está habilitado a realizar codificação ao vivo com serviços de media. Os fluxos ingeridos passam por Live Events sem qualquer processamento adicional. Este método **chama-se passagem.** Recomendamos que o codificadora ao vivo envie streams multi-bitrates em vez de um fluxo de bitrate para um evento ao vivo de passagem para permitir o streaming de bitrate adaptativo para o cliente. 

    Se estiver a utilizar streams multi-bitrates para o evento ao vivo, o tamanho do VÍDEO GOP e os fragmentos de vídeo em diferentes bitrates devem ser sincronizados para evitar comportamentos inesperados no lado da reprodução.

  > [!TIP]
  > A utilização de um método de passagem é a forma mais económica de fazer streaming ao vivo.
 
* Um codificador ao vivo no local envia um fluxo de bitrate único para o Live Event que está habilitado a realizar codificação ao vivo com os Media Services num dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O Live Event executa então a codificação ao vivo do fluxo de bitrate único para um stream de vídeo multi-bitrate (adaptável).

Este artigo discute codificadores de streaming em direto verificados no local. A verificação é feita através da auto-verificação do fornecedor ou da verificação do cliente. O Microsoft Azure Media Services não faz testes completos ou rigorosos de cada codificação e não verifica continuamente as atualizações. Para obter instruções sobre como verificar o seu codificadora ao vivo no local, consulte [o codificadora no local](become-on-premises-encoder-partner.md)

Para obter informações detalhadas sobre codificação ao vivo com os Media Services, consulte [o streaming ao vivo com os Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Requisitos do codificadores

Os codificadores devem suportar TLS 1.2 quando utilizarem protocolos HTTPS ou RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Codificadores ao vivo que desempenham RTMP

Os Serviços de Multimédia recomendam utilizar um dos codificadores em direto seguintes que têm RTMP como saída. Os sistemas de URL suportados são `rtmp://` ou `rtmps://` .

Ao transmitir em fluxo através de RTMP, verifique as definições da firewall e/ou do proxy, para confirmar que as portas TCP de saída 1935 e 1936 estão abertas.<br/><br/>
Ao transmitir em fluxo através de RTMPS, verifique as definições da firewall e/ou do proxy, para confirmar que as portas TCP de saída 2935 e 2936 estão abertas.

> [!NOTE]
> Os codificadores devem suportar o TLS 1.2 quando utilizarem os protocolos RTMPS.

- Adobe Flash Media Live Encoder 3.2
- [Antix Digital](http://www.antixdigital.com/) StreamZ Live (anteriormente Imagine Communication SelenioFlex Live)
- [Blackmagic ATEM Mini e ATEM Mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Ao Vivo 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (versão 2.14.15 e superior)
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Herói 7 e Herói 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Telestream Wirecast (versão 13.0.2 ou superior devido ao requisito TLS 1.2)
- Telestream Wirecast S (apenas o RTMP está suportado. Sem suporte RTMPS por falta de TLS 1.2+)
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> A lista acima dos codificadores é apenas uma lista de recomendações. Os codificadores não são testados ou validados pela Microsoft numa base contínua e as atualizações ou alterações de rutura podem ser introduzidas por fornecedores de codificadores ou projetos de código aberto que possam quebrar a compatibilidade. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Codificadores ao vivo que desempenaram MP4 (Smooth Streaming ingest)

Os Media Services recomendam a utilização de um dos seguintes codificadores ao vivo que tenham o streaming liso multi-bitrate (MP4 fragmentado) como saída. Os sistemas de URL suportados são `http://` ou `https://` .

> [!NOTE]
> Os codificadores devem suportar o TLS 1.2 quando utilizarem os protocolos HTTPS.

- Ateme TITAN Live
- [Antix Digital](http://www.antixdigital.com/) StreamZ Live (anteriormente Imagine Communication SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Elemental Live (versão 2.14.15 e superior devido ao requisito TLS 1.2)
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Media Excel Hero Live and Hero 4K (UHD/HEVC)

> [!TIP]
>  Se estiver a transmitir eventos ao vivo em várias línguas (por exemplo, uma faixa áudio inglesa e uma faixa áudio espanhola), pode fazê-lo com o codificadora ao vivo media Excel configurado para enviar o feed ao vivo para um Live Event.

> [!WARNING]
> A lista acima dos codificadores é apenas uma lista de recomendações. Os codificadores não são testados ou validados pela Microsoft numa base contínua e o suporte ou bugs podem ser introduzidos pelos fornecedores de codificadores ou projetos de código aberto que quebram a compatibilidade a qualquer momento. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Configuração de configurações de codificadores ao vivo no local

Para obter informações sobre quais as configurações válidas para o seu tipo de evento ao vivo, consulte [a comparação de tipos de Eventos Vivos.](live-event-types-comparison.md)

### <a name="playback-requirements"></a>Requisitos de reprodução

Para reproduzir conteúdo, tanto um stream de áudio como de vídeo deve estar presente. A reprodução do stream apenas de vídeo não é suportada.

### <a name="configuration-tips"></a>Sugestões de configuração

- Sempre que possível, utilize uma ligação à Internet por fios.
- Quando estiveres a determinar os requisitos de largura de banda, duplica os bitrates de streaming. Embora não seja obrigatória, esta regra simples ajuda a mitigar o impacto do congestionamento da rede.
- Ao utilizar codificadores baseados em software, feche quaisquer programas desnecessários.
- Alterar a configuração do codificador depois de ter começado a empurrar tem efeitos negativos no evento. As alterações de configuração podem fazer com que o evento se torne instável. 
- Teste e valida sempre as versões mais recentes do software codificador para uma compatibilidade contínua com a Azure Media Services. A Microsoft não revaliona os codificadores nesta lista, e a maioria das validações são feitas pelos fornecedores de software diretamente como uma "auto-certificação".
- Certifique-se de que dá tempo suficiente para preparar o seu evento. Para eventos de grande escala, recomendamos iniciar a configuração uma hora antes do seu evento.
- Utilize o vídeo H.264 e a saída de código de áudio AAC-LC.
- Cinge-te a resoluções e taxas de fotogramas apoiadas para o tipo de Live Event para o qual estás a transmitir (por exemplo, 60fps é atualmente rejeitado.)
- Certifique-se de que existe quadro chave ou alinhamento temporal gop entre qualidades de vídeo.
- Certifique-se de que existe um nome de fluxo único para cada qualidade de vídeo.
- Utilize uma codificação rigorosa do CBR recomendada para um ótimo desempenho de bitrate adaptativo.

> [!IMPORTANT]
> Observe a condição física da máquina (CPU / Memória / etc) pois o upload de fragmentos para a nuvem envolve operações de CPU e IO. Se alterar quaisquer definições no codificador, certifique-se de que repor os canais /evento ao vivo para que a alteração produza efeitos.

## <a name="see-also"></a>Ver também

[Streaming em direto com media services v3](live-streaming-overview.md)

## <a name="next-steps"></a>Passos seguintes

[Como verificar o seu codificação](become-on-premises-encoder-partner.md)

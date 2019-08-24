---
title: Visão geral do empacotamento dinâmico dos serviços de mídia do Azure | Microsoft Docs
description: O artigo fornece uma visão geral do empacotamento dinâmico nos serviços de mídia do Azure.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/22/2019
ms.author: juliako
ms.openlocfilehash: 352b42099bcd832792aad2fa24dca3e14525dc06
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990634"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

Serviços de Mídia do Microsoft Azure pode ser usado para codificar vários formatos de arquivo de origem de mídia e fornecê-los por meio de protocolos de streaming diferentes, com ou sem proteção de conteúdo, para alcançar todos os principais dispositivos (por exemplo, dispositivos iOS e Android). Esses clientes entendem protocolos diferentes, por exemplo, o iOS requer que os fluxos sejam entregues no formato HTTP Live Streaming (HLS) e dispositivos Android ofereçam suporte a HLS, bem como MPEG DASH. Para preparar os arquivos de origem para entrega por streaming de taxa de bits adaptável, eles [](encoding-concept.md) precisam ser codificados em um conjunto de arquivos de taxa de bits múltiplas (também chamados de taxa de bits adaptável) MP4 (ISO Base Media 14496-12). A partir desse conjunto de arquivos MP4, você pode fornecer vídeo por meio de protocolos HLS ou MPEG-DASH ou Smooth Streaming usando o **empacotamento dinâmico**.

Nos serviços de mídia, um [ponto de extremidade de streaming](streaming-endpoint-concept.md) representa um serviço de empacotamento e origem (just-in-time) dinâmico que pode entregar seu conteúdo ao vivo e sob demanda diretamente a um aplicativo de player de cliente, usando um dos protocolos de mídia de streaming comuns (HLS ou DASH). O empacotamento dinâmico é um recurso que é fornecido por padrão em todos os **pontos de extremidade de streaming** (Standard ou Premium). 

Para aproveitar o **empacotamento dinâmico**, você precisa ter um **ativo** com um conjunto de arquivos MP4 de taxa de bits adaptável e arquivos de configuração de streaming necessários para o empacotamento dinâmico dos serviços de mídia. Uma forma de obter os ficheiros é codificar o ficheiro mezzanine (de origem) com os Serviços de Multimédia. Para disponibilizar vídeos no ativo codificado para os clientes para reprodução, você precisa criar um localizador de **streaming** e URLs de streaming de compilação. Em seguida, com base no formato especificado no manifesto do cliente de streaming (HLS, MPEG DASH ou Smooth Streaming), você receberá o fluxo no protocolo escolhido.

Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente. 

Nos serviços de mídia, o empacotamento dinâmico é usado se você está transmitindo vídeos ao vivo ou sob demanda. 

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="on-demand-streaming-workflow"></a>Fluxo de trabalho de streaming por demanda

Veja a seguir um fluxo de trabalho de streaming dos serviços de mídia comum em que o empacotamento dinâmico é usado junto com o codificador padrão nos serviços de mídia do Azure.

1. Carregue um arquivo de entrada, como um arquivo QuickTime/MOV ou MXF (para obter a lista de formatos com suporte [, consulte formatos com suporte pelo Media Encoder Standard](media-encoder-standard-formats.md). Isso também é conhecido como o arquivo de origem ou de mezanino.
1. [Codifique](#encode-to-adaptive-bitrate-mp4s) seu arquivo de mezanino em um conjunto de taxa de bits adaptável do H. 264/AAC. 
1. Publique o ativo de saída que contém o conjunto de MP4 de taxa de bits adaptável. Você publica criando um localizador de streaming.
1. Crie URLs que visam formatos diferentes (HLS, MPEG-DASH e Smooth Streaming). O **ponto de extremidade de streaming** cuidaria de servir o manifesto correto e as solicitações para todos esses formatos diferentes.

O diagrama a seguir mostra o streaming sob demanda com fluxo de trabalho de empacotamento dinâmico.

![Diagrama de um fluxo de trabalho para streaming sob demanda com empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar para MP4s de taxa de bits adaptável

Os artigos a seguir mostram exemplos de [como codificar um vídeo com os serviços de mídia](encoding-concept.md):

* [Codificar de uma URL HTTPS usando predefinições internas](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando predefinições internas](job-input-from-local-file-how-to.md)
* [Crie uma predefinição personalizada para direcionar seu cenário específico ou requisitos de dispositivo](customize-encoder-presets-how-to.md)

Consulte a lista de [codecs e formatos](media-encoder-standard-formats.md)de Media Encoder Standard.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de transmissão ao vivo

Um evento ao vivo pode ser um dos dois tipos: codificação de passagem ou ativa. 

Veja um fluxo de trabalho comum para transmissão ao vivo com empacotamento dinâmico:

1. Crie um [evento ao vivo](live-events-outputs-concept.md).
1. Obtenha a URL de ingestão e configure seu codificador local para usar a URL para enviar o feed de contribuição.
1. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo recebida.
1. Crie um novo ativo.
1. Crie uma saída ao vivo e use o nome do ativo que você criou.<br />A saída ao vivo arquiva o fluxo no ativo.
1. Crie um localizador de streaming com os tipos de política de streaming internos.<br />Se você pretende criptografar seu conteúdo, examine [visão geral da proteção de conteúdo](content-protection-overview.md).
1. Liste os caminhos no localizador de streaming para obter as URLs a serem usadas.
1. Obtenha o nome do host para o ponto de extremidade de streaming do qual você deseja transmitir.
1. Crie URLs que visam formatos diferentes (HLS, MPEG-DASH e Smooth Streaming). O ponto de extremidade de streaming cuida do fornecimento do manifesto e das solicitações corretas para os diferentes formatos.

Este diagrama mostra o fluxo de trabalho para transmissão ao vivo com empacotamento dinâmico:

![Diagrama de um fluxo de trabalho para codificação de passagem com empacotamento dinâmico](./media/live-streaming/pass-through.svg)

Para obter informações sobre a transmissão ao vivo nos serviços de mídia v3, consulte [visão geral da transmissão ao vivo](live-streaming-overview.md).

## <a name="delivery-protocols"></a>Protocolos de entrega

Você pode usar esses protocolos de entrega para seu conteúdo no empacotamento dinâmico dos serviços de mídia:

|Protocol|Exemplo|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|CSF MPEG-DASH| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|CMAF MPEG-DASH|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Transmissão em Fluxo Uniforme| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codecs de vídeo com suporte do empacotamento dinâmico

O empacotamento dinâmico dá suporte aos seguintes codecs de vídeo:
* Arquivos MP4, que contêm vídeo que é codificado com [H. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1) ou [H. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, HEV1 ou hvc1).

> [!NOTE]
> As resoluções de até 4K e as taxas de quadros de até 60 quadros/segundo foram testadas com o empacotamento dinâmico. O [codificador Premium](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) dá suporte à codificação para H. 265, por meio das APIs v2 herdadas. Entre em amshelp@microsoft.com contato com se você tiver dúvidas sobre este tópico. 

## <a name="a-idaudio-codecsaudio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Codecs de áudio com suporte do empacotamento dinâmico

O empacotamento dinâmico dá suporte aos seguintes protocolos de áudio descritos abaixo:

* Arquivos MP4
* Várias faixas de áudio

O empacotamento dinâmico não dá suporte a arquivos que contêm áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (é um codec herdado).

> [!NOTE]
> O [codificador Premium](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) dá suporte à codificação para Dolby Digital Plus, por meio das APIs v2 herdadas. Entre em amshelp@microsoft.com contato com se você tiver dúvidas sobre este tópico. 

### <a name="mp4-files"></a>Arquivos MP4

O empacotamento dinâmico dá suporte a arquivos MP4, que contêm áudio codificado com os seguintes protocolos: 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 ou HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Avançado AC-3 ou E-AC3)
* Dolby Atmos<br />
   O conteúdo Dolby Atmos do streaming tem suporte para padrões como o protocolo MPEG-DASH com o formato de streaming comum (CSF) ou o MP4 fragmentado do CMAF (formato de aplicativo de mídia comum) e via HTTP Live Streaming (HLS) com CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Os codecs DTS com suporte nos formatos de empacotamento DASH-CSF, DASH-CMAF, HLS-M2TS e HLS-CMAF são:  

    * Dtsc (surround digital de DTS)
    * DTS-alta resolução de HD e DTS-áudio mestre de HD (dtsh)
    * DTS Express (dtse)
    * DTS-HD lossless (sem núcleos) (DTSL)

### <a name="multiple-audio-tracks"></a>Várias faixas de áudio

O empacotamento dinâmico dá suporte a faixas de vários áudio para saída de HLS (versão 4 ou posterior) para ativos de streaming que têm várias faixas de áudio com vários codecs e linguagens.

## <a name="manifests"></a>Manifestos 
 
No empacotamento dinâmico dos serviços de mídia, os manifestos do cliente de streaming para HLS, MPEG-DASH e Smooth Streaming são gerados dinamicamente com base no seletor de formato na URL. Para obter mais informações, consulte [protocolos de entrega](#delivery-protocols). 

Um arquivo de manifesto inclui metadados de streaming, como o tipo de faixa (áudio, vídeo ou texto), o nome da faixa, a hora de início e de término, a taxa de bits (qualidades), os idiomas de controle, a janela de apresentação (janela deslizante de duração fixa) e o codec de vídeo (FourCC). Ele também instrui o Player a recuperar o próximo fragmento, fornecendo informações sobre os próximos fragmentos de vídeo reproduzidos que estão disponíveis e sua localização. Fragmentos (ou segmentos) são as "partes" reais do conteúdo de vídeo.

### <a name="examples"></a>Exemplos

#### <a name="hls"></a>HLS

Aqui está um exemplo de um arquivo de manifesto HLS, também chamado de lista de reprodução mestre HLS: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

Aqui está um exemplo de um arquivo de manifesto MPEG-DASH, também chamado de uma descrição de apresentação de mídia MPEG-DASH (MPD):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

Aqui está um exemplo de um arquivo de manifesto Smooth Streaming:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>Nomenclatura de faixas no manifesto

Se um nome de faixa de áudio for especificado no arquivo. ISM, os serviços de `Label` mídia adicionarão `AdaptationSet` um elemento em um para especificar as informações de textural para a faixa de áudio específica. Um exemplo do manifesto de traço de saída:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

O player pode usar o `Label` elemento para exibir em sua interface do usuário.

### <a name="signaling-audio-description-tracks"></a>Sinalizando faixas de descrição de áudio

Um cliente pode anotar uma faixa de áudio como descrição de áudio no manifesto. Para fazer isso, eles adicionariam os parâmetros "Accessibility" e "role" ao arquivo. ISM. Os serviços de mídia reconhecerão a descrição de áudio se uma faixa de áudio tiver o parâmetro "Accessibility" com o valor "Description" e param "role" com o valor "Alternate". Se os serviços de mídia detectarem a descrição de áudio no arquivo. ISM, as informações de descrição de áudio serão passadas `Role="alternate"` para o manifesto `StreamIndex` do cliente como `Accessibility="description"` atributos e no elemento.

Se a combinação de "acessibilidade" = "Descrição" e "função" = "alternativo" estiver definida no arquivo. ISM, o manifesto de traço e os valores de manifesto de Smooth serão definidos conforme definido nos parâmetros de "acessibilidade" e "função". É responsabilidade do cliente definir esses dois valores certos e marcar uma faixa de áudio como descrição de áudio. Por especificação de traço, "acessibilidade" = "Descrição" e "função" = "alternativo" em conjunto significa que uma faixa de áudio é uma descrição de áudio.

Para HLS v7 e posterior (`format=m3u8-cmaf`), sua lista de `CHARACTERISTICS="public.accessibility.describes-video"` reprodução só transporta quando a combinação de "acessibilidade" = "Descrição" e "função" = "alternativo" é definida no arquivo. ISM. 

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviadas aos players, você pode usar a filtragem dinâmica com o empacotador dinâmico dos serviços de mídia. Para obter mais informações, consulte [pré-filtrando manifestos com o Gerenciador dinâmico](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Encriptação dinâmica

Você pode usar a *criptografia dinâmica* para criptografar dinamicamente seu conteúdo em tempo real ou sob demanda com o AES-128 ou qualquer um dos três principais sistemas de DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço para a entrega de chaves AES e licenças DRM para clientes autorizados. Para obter mais informações, consulte [criptografia dinâmica](content-protection-overview.md).

## <a name="more-information"></a>Mais informações

Confira a [Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md).


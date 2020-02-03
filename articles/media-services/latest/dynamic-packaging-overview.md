---
title: Empacotamento dinâmico nos serviços de mídia do Azure v3
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do empacotamento dinâmico nos serviços de mídia do Azure.
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
ms.date: 01/23/2020
ms.author: juliako
ms.openlocfilehash: 3984f33cd97ada9b3d5301e45fe3506966880848
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719675"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Empacotamento dinâmico nos serviços de mídia v3

Serviços de Mídia do Microsoft Azure pode ser usado para codificar muitos formatos de arquivo de origem de mídia. Ele os entrega por meio de protocolos de streaming diferentes, com ou sem proteção de conteúdo, para alcançar todos os principais dispositivos (como dispositivos iOS e Android). Esses clientes entendem protocolos diferentes. Por exemplo, o iOS requer que os fluxos sejam entregues no formato HTTP Live Streaming (HLS) e os dispositivos Android dão suporte a HLS, bem como MPEG DASH.

Nos Serviços de Media, um [Streaming Endpoint](streaming-endpoint-concept.md) representa um serviço dinâmico de embalagem e origem (just-in-time) que pode entregar os seus conteúdos ao vivo e a pedido diretamente a uma aplicação de jogador de clientes. Ele usa um dos protocolos de mídia de streaming comuns mencionados na seção a seguir. O Empacotamento Dinâmico é uma funcionalidade padrão em todos os Pontos Finais de Transmissão em Fluxo (Standard ou Premium).

## <a name="a-iddelivery-protocolsto-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>Para preparar os seus ficheiros de origem para entrega

Para tirar partido da Embalagem Dinâmica, é necessário [codificar](encoding-concept.md) o seu ficheiro mezanino (fonte) num conjunto de ficheiros MP4 (ISO Base Media 14496-12). É necessário ter um [Ativo](assets-concept.md) com o MP4 codificado e ficheiros de configuração de streaming necessários pela Media Services Dynamic Packaging. A partir deste conjunto de ficheiros MP4, pode utilizar a Dynamic Packaging para entregar vídeo através dos protocolos de streaming de mídia descritos abaixo.

> [!TIP]
> Uma forma de obter os ficheiros de configuração MP4 e streaming é [codificar o seu ficheiro mezanino com os Media Services](#encode-to-adaptive-bitrate-mp4s). 

Para disponibilizar vídeos no ativo codificado aos clientes para reprodução, tem de criar um Localizador de [Streaming](streaming-locators-concept.md) e construir URLs de streaming. Em seguida, com base no formato especificado no manifesto do cliente de streaming (HLS, MPEG DASH ou Smooth Streaming), você receberá o fluxo no protocolo escolhido.

Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

Se planeia proteger o seu conteúdo utilizando encriptação dinâmica dos Media Services, consulte [protocolos de streaming e tipos](content-protection-overview.md#streaming-protocols-and-encryption-types)de encriptação .

### <a name="hls-protocol"></a>Protocolo HLS

O seu cliente de streaming pode especificar os seguintes formatos HLS:

|Protocol|Exemplo|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>Protocolo MPEG-DASH

O seu cliente de streaming pode especificar os seguintes formatos MPEG-DASH:

|Protocol|Exemplo|
|---|---|
|CSF MPEG-DASH| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|CMAF MPEG-DASH|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Protocolo de Streaming Suave

O seu cliente de streaming pode especificar os seguintes formatos Smooth Streaming:

|Protocol|Notas/exemplos| 
|---|---|
|Transmissão em Fluxo Uniforme| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2.0 (manifesto legado)|Por padrão, Smooth Streaming formato de manifesto contém a marca de repetição (r-tag). No entanto, alguns jogadores não apoiam a `r-tag`. Os clientes com esses players podem usar um formato que desabilita a marca r:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

## <a name="on-demand-streaming-workflow"></a>Fluxo de trabalho de streaming por demanda

As etapas a seguir mostram um fluxo de trabalho de streaming dos serviços de mídia comum em que o empacotamento dinâmico é usado junto com o codificador padrão nos serviços de mídia do Azure.

1. Carregue um arquivo de entrada, como um arquivo QuickTime/MOV ou MXF. Esse arquivo também é chamado de mezanino ou arquivo de origem. Para a lista de formatos suportados, consulte [Formatos Suportados pela Norma Media Encoder](media-encoder-standard-formats.md).
1. [Codifique](#encode-to-adaptive-bitrate-mp4s) o seu ficheiro mezanino num conjunto de bitrate adaptativo H.264/AAC MP4.
1. Publique o ativo de saída que contém o conjunto de MP4 de taxa de bits adaptável. Você publica criando um localizador de streaming.
1. Crie URLs que visam formatos diferentes (HLS, MPEG-DASH e Smooth Streaming). O **Streaming Endpoint** cuidaria de servir o manifesto correto e pedidos para todos estes diferentes formatos.

O diagrama a seguir mostra o streaming sob demanda com fluxo de trabalho de empacotamento dinâmico.

![Diagrama de um fluxo de trabalho para streaming sob demanda com empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar para MP4s de taxa de bits adaptável

Os seguintes artigos mostram exemplos de [como codificar um vídeo com os Media Services:](encoding-concept.md)

* [Codificar a partir de um URL HTTPS utilizando predefinições incorporadas](job-input-from-http-how-to.md).
* [Codificar um ficheiro local utilizando predefinições incorporadas](job-input-from-local-file-how-to.md).
* [Construa um predefinido personalizado para direcionar o seu cenário ou requisitos específicos do dispositivo](customize-encoder-presets-how-to.md).

Consulte a lista de [formatos e códigos](media-encoder-standard-formats.md)Padrão Media Encoder .

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de transmissão ao vivo

Um evento ao vivo pode ser um dos dois tipos: codificação de passagem ou ativa. 

Veja um fluxo de trabalho comum para transmissão ao vivo com empacotamento dinâmico:

1. Criar um [evento ao vivo.](live-events-outputs-concept.md)
1. Obtenha a URL de ingestão e configure seu codificador local para usar a URL para enviar o feed de contribuição.
1. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo recebida.
1. Crie um novo ativo.
1. Crie uma saída ao vivo e use o nome do ativo que você criou.<br />A saída ao vivo arquiva o fluxo no ativo.
1. Crie um localizador de streaming com os tipos de política de streaming internos.<br />Se pretender encriptar o seu conteúdo, reveja a visão geral da [proteção](content-protection-overview.md)do conteúdo .
1. Liste os caminhos no localizador de streaming para obter as URLs a serem usadas.
1. Obtenha o nome do host para o ponto de extremidade de streaming do qual você deseja transmitir.
1. Crie URLs que visam formatos diferentes (HLS, MPEG-DASH e Smooth Streaming). O ponto de extremidade de streaming cuida do fornecimento do manifesto e das solicitações corretas para os diferentes formatos.

Este diagrama mostra o fluxo de trabalho para transmissão ao vivo com empacotamento dinâmico:

![Diagrama de um fluxo de trabalho para codificação de passagem com empacotamento dinâmico](./media/live-streaming/pass-through.svg)

Para obter informações sobre streaming ao vivo em Media Services v3, consulte a [visão geral do streaming ao vivo](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codecs de vídeo com suporte do empacotamento dinâmico

A Dynamic Packaging suporta ficheiros MP4 que contenham vídeo codificado com [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1) ou [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

> [!NOTE]
> As resoluções de até 4K e taxas de quadros de até 60 quadros/segundo foram testadas com o empacotamento dinâmico. O [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) suporta a codificação para H.265 através do legado v2 APIs.

## <a name="a-idaudio-codecsaudio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Codecs áudio suportados por Embalagens Dinâmicas

O empacotamento dinâmico dá suporte a áudio codificado com os seguintes protocolos:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 ou HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (AC-3 ou E-AC3 melhorado)
* Dolby Atmos<br />
   O conteúdo Dolby Atmos do streaming tem suporte para padrões como o protocolo MPEG-DASH com o formato de streaming comum (CSF) ou o MP4 fragmentado do CMAF (formato de aplicativo de mídia comum) e via HTTP Live Streaming (HLS) com CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Os codecs DTS com suporte nos formatos de empacotamento DASH-CSF, DASH-CMAF, HLS-M2TS e HLS-CMAF são:  

    * Dtsc (surround digital de DTS)
    * DTS-alta resolução de HD e DTS-áudio mestre de HD (dtsh)
    * DTS Express (dtse)
    * DTS-HD lossless (sem núcleos) (DTSL)

O empacotamento dinâmico dá suporte a várias faixas de áudio com DASH ou HLS (versão 4 ou posterior) para streaming de ativos que têm várias faixas de áudio com vários codecs e idiomas.

### <a name="additional-notes"></a>Notas adicionais

A Dynamic Packaging não suporta ficheiros que contenham áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (é um codec legado).

> [!NOTE]
> O [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) suporta a codificação para dolby Digital Plus através do legado v2 APIs.

## <a name="manifests"></a>Manifestos

No empacotamento dinâmico dos serviços de mídia, os manifestos do cliente de streaming para HLS, MPEG-DASH e Smooth Streaming são gerados dinamicamente com base no seletor de formato na URL.  

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

Se um nome de faixa de áudio for especificado no ficheiro .ism, a Media Services adiciona um elemento `Label` dentro de um `AdaptationSet` para especificar as informações texturais para a faixa de áudio específica. Um exemplo do manifesto DASH de saída:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

O leitor pode utilizar o elemento `Label` para exibir no seu UI.

### <a name="signaling-audio-description-tracks"></a>Sinalizando faixas de descrição de áudio

Você pode adicionar uma faixa de narração ao vídeo para ajudar os clientes com deficiência visual a seguir a gravação de vídeo ouvindo a narração. Você precisa anotar uma faixa de áudio como descrição de áudio no manifesto. Para fazer isso, adicione os parâmetros "Accessibility" e "role" ao arquivo. ISM. É sua responsabilidade definir esses parâmetros corretamente para sinalizar uma faixa de áudio como descrição de áudio. Por exemplo, adicione `<param name="accessibility" value="description" />` e `<param name="role" value="alternate"` ao ficheiro .ism para uma faixa de áudio específica. 

Para mais informações, consulte o Como sinalizar um exemplo descritivo da [faixa áudio.](signal-descriptive-audio-howto.md)

#### <a name="smooth-streaming-manifest"></a>Manifesto Smooth Streaming

Se estiver a reproduzir um stream de Streaming Suave, o manifesto transportará valores em `Accessibility` e `Role` atributos para essa faixa de áudio. Por exemplo, `Role="alternate" Accessibility="description"` seria adicionado no elemento `StreamIndex` para indicar que é uma descrição áudio.

#### <a name="dash-manifest"></a>Manifesto DASH

Para o manifesto DASH, os dois elementos a seguir seriam adicionados para sinalizar a descrição do áudio:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS playlist

Para hls v7 e acima `(format=m3u8-cmaf)`, a sua lista de reprodução transportaria `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` quando a faixa de descrição áudio for sinalizada.

#### <a name="example"></a>Exemplo

Para mais informações, consulte [Como sinalizar faixas de descrição áudio](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviadas aos players, você pode usar a filtragem dinâmica com o empacotador dinâmico dos serviços de mídia. Para mais informações, consulte [os manifestos de pré-filtragem com o embalador dinâmico](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Encriptação dinâmica

Pode utilizar *encriptação dinâmica* para encriptar dinamicamente os seus conteúdos ao vivo ou a pedido com a AES-128 ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço para a entrega de chaves AES e licenças DRM para clientes autorizados. Para mais informações, consulte [encriptação dinâmica](content-protection-overview.md).

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="more-information"></a>Mais informações

Consulte a [comunidade azure media services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de apoio .

## <a name="next-steps"></a>Passos Seguintes

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

Saiba como [carregar, codificar e transmitir vídeos.](stream-files-tutorial-with-api.md)

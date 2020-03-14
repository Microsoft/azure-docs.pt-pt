---
title: Embalagem dinâmica em Azure Media Services v3
titleSuffix: Azure Media Services
description: Este artigo dá uma visão geral da embalagem dinâmica nos Serviços Azure Media.
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
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: d408a862c18038f64b816bb54fc235d1b9d84179
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240381"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Embalagem dinâmica em Media Services v3

O Microsoft Azure Media Services pode ser usado para codificar muitos formatos de ficheiros de fonte de mídia. Fornece-os através de diferentes protocolos de streaming, com ou sem proteção de conteúdos, para chegar a todos os principais dispositivos (como dispositivos iOS e Android). Estes clientes entendem diferentes protocolos. Por exemplo, o iOS requer streams para serem entregues em http live streaming (HLS) e dispositivos Android suportam HLS, bem como MPEG DASH.

Nos Serviços de Media, um [Streaming Endpoint](streaming-endpoint-concept.md) representa um serviço dinâmico de embalagem e origem (just-in-time) que pode entregar os seus conteúdos ao vivo e a pedido diretamente a uma aplicação de jogador de clientes. Utiliza um dos protocolos comuns de streaming de meios de comunicação mencionados na secção seguinte. O Empacotamento Dinâmico é uma funcionalidade padrão em todos os Pontos Finais de Transmissão em Fluxo (Standard ou Premium).

> [!NOTE]
> Pode utilizar o [portal Azure](https://portal.azure.com/) para gerir o V3 [Live Events,](live-events-outputs-concept.md)ver v3 [Assets,](assets-concept.md)obter informações sobre o acesso a APIs. Para todas as outras tarefas de gestão (por exemplo, Transforms and Jobs), utilize o [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados .

## <a name="a-iddelivery-protocolsto-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>Para preparar os seus ficheiros de origem para entrega

Para tirar partido da Embalagem Dinâmica, é necessário [codificar](encoding-concept.md) o seu ficheiro mezanino (fonte) num conjunto de ficheiros MP4 (ISO Base Media 14496-12). É necessário ter um [Ativo](assets-concept.md) com o MP4 codificado e ficheiros de configuração de streaming necessários pela Media Services Dynamic Packaging. A partir deste conjunto de ficheiros MP4, pode utilizar a Dynamic Packaging para entregar vídeo através dos protocolos de streaming de mídia descritos abaixo.

> [!TIP]
> Uma forma de obter os ficheiros de configuração MP4 e streaming é [codificar o seu ficheiro mezanino com os Media Services](#encode-to-adaptive-bitrate-mp4s). 

Para disponibilizar vídeos no ativo codificado aos clientes para reprodução, tem de criar um Localizador de [Streaming](streaming-locators-concept.md) e construir URLs de streaming. Em seguida, com base no formato especificado no manifesto do cliente de streaming (HLS, MPEG DASH ou Smooth Streaming), recebe o stream no protocolo que escolheu.

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
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Protocolo de Streaming Suave

O seu cliente de streaming pode especificar os seguintes formatos Smooth Streaming:

|Protocol|Notas/exemplos| 
|---|---|
|Transmissão em Fluxo Uniforme| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Smooth Streaming 2.0 (manifesto legado)|Por predefinição, o formato manifesto Smooth Streaming contém a etiqueta de repetição (r-tag). No entanto, alguns jogadores não apoiam a `r-tag`. Os clientes com estes jogadores podem usar um formato que desativa a r-tag:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

## <a name="on-demand-streaming-workflow"></a>Fluxo de trabalho de streaming a pedido

Os seguintes passos mostram um fluxo de trabalho de streaming de Serviços de Media comuns onde a Dynamic Packaging é utilizada juntamente com o Encoder Standard nos Serviços Azure Media.

1. Faça upload de um ficheiro de entrada, como um ficheiro QuickTime/MOV ou MXF. Este ficheiro também é referido como o mezanino ou ficheiro fonte. Para a lista de formatos suportados, consulte [Formatos Suportados pela Norma Media Encoder](media-encoder-standard-formats.md).
1. [Codifique](#encode-to-adaptive-bitrate-mp4s) o seu ficheiro mezanino num conjunto de bitrate adaptativo H.264/AAC MP4.
1. Publique o ativo de saída que contém o conjunto de MP4 bitrate adaptativo. Publica criando um localizador de streaming.
1. Construa URLs que visam diferentes formatos (HLS, MPEG-DASH e Smooth Streaming). O **Streaming Endpoint** cuidaria de servir o manifesto correto e pedidos para todos estes diferentes formatos.

O diagrama seguinte mostra o streaming a pedido com fluxo de trabalho de Embalagem Dinâmica.

![Diagrama de um fluxo de trabalho para streaming a pedido com Embalagem Dinâmica](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar a MP4s bitrate adaptativo

Os seguintes artigos mostram exemplos de [como codificar um vídeo com os Media Services:](encoding-concept.md)

* [Codificar a partir de um URL HTTPS utilizando predefinições incorporadas](job-input-from-http-how-to.md).
* [Codificar um ficheiro local utilizando predefinições incorporadas](job-input-from-local-file-how-to.md).
* [Construa um predefinido personalizado para direcionar o seu cenário ou requisitos específicos do dispositivo](customize-encoder-presets-how-to.md).

Consulte a lista de [formatos e códigos](media-encoder-standard-formats.md)Padrão Media Encoder .

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Um evento ao vivo pode ser definido para um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo de bitrate). 

Aqui está um fluxo de trabalho comum para streaming ao vivo com Embalagens Dinâmicas:

1. Criar um [evento ao vivo.](live-events-outputs-concept.md)
1. Obtenha o URL ingerir e configure o seu codificador no local para utilizar o URL para enviar o feed de contribuição.
1. Obtenha o URL de pré-visualização e use-o para verificar se a entrada do codificador está a ser recebida.
1. Criar um novo ativo.
1. Crie uma saída ao vivo e use o nome de ativo que criou.<br />A saída ao vivo arquiva o fluxo no ativo.
1. Crie um localizador de streaming com os tipos de política de streaming incorporados.<br />Se pretender encriptar o seu conteúdo, reveja a visão geral da [proteção](content-protection-overview.md)do conteúdo .
1. Enumere os caminhos do localizador de streaming para que os URLs utilizem.
1. Obtenha o nome do anfitrião para o ponto final de streaming que pretende transmitir.
1. Construa URLs que visam diferentes formatos (HLS, MPEG-DASH e Smooth Streaming). O ponto final de streaming cuida de servir o manifesto correto e pedidos para os diferentes formatos.

Este diagrama mostra o fluxo de trabalho para streaming ao vivo com Embalagem Dinâmica:

![Diagrama de um fluxo de trabalho para codificação pass-through com embalagem dinâmica](./media/live-streaming/pass-through.svg)

Para obter informações sobre streaming ao vivo em Media Services v3, consulte a [visão geral do streaming ao vivo](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Códigos de vídeo suportados por Embalagens Dinâmicas

A Dynamic Packaging suporta ficheiros MP4 que contenham vídeo codificado com [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1) ou [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

> [!NOTE]
> Resoluções de até 4K e taxas de fotogramas de até 60 fotogramas/segundo foram testadas com Embalagens Dinâmicas. O [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) suporta a codificação para H.265 através do legado v2 APIs.

## <a name="a-idaudio-codecsaudio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>Codecs áudio suportados por Embalagens Dinâmicas

A Dynamic Packaging suporta áudio codificado com os seguintes protocolos:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 ou HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (AC-3 ou E-AC3 melhorado)
* Dolby Atmos<br />
   O conteúdo do Streaming Dolby Atmos é suportado para normas como o protocolo MPEG-DASH com formato de streaming comum (CSF) ou formato de aplicação de mídia comum (CMAF) fragmentado MP4, e via HTTP Live Streaming (HLS) com CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Os códigos DTS suportados pelos formatos de embalagem DASH-CSF, DASH-CMAF, HLS-M2TS e HLS-CMAF são:  

    * DTS Surround Digital (dtsc)
    * Alta Resolução DTS-HD e Áudio Master DTS-HD (dtsh)
    * DTS Express (dtse)
    * DTS-HD Lossless (sem núcleo) (dtsl)

A Dynamic Packaging suporta várias faixas de áudio com DASH ou HLS (versão 4 ou posterior) para streaming de ativos que têm múltiplas faixas de áudio com vários códigos e idiomas.

### <a name="additional-notes"></a>Notas adicionais

A Dynamic Packaging não suporta ficheiros que contenham áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (é um codec legado).

> [!NOTE]
> O [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) suporta a codificação para dolby Digital Plus através do legado v2 APIs.

## <a name="manifests"></a>Manifestos

Na Media Services Dynamic Packaging, os manifestos do cliente de streaming para HLS, MPEG-DASH e Smooth Streaming são gerados dinamicamente com base no seletor de formato no URL.  

Um ficheiro manifesto inclui metadados de streaming como tipo de faixa (áudio, vídeo ou texto), nome da faixa, tempo de início e fim, bitrate (qualidades), linguagem de faixa, janela de apresentação (janela deslizante de duração fixa) e código de vídeo (FourCC). Também instrui o jogador a recuperar o próximo fragmento fornecendo informações sobre os próximos fragmentos de vídeo jogáveis que estão disponíveis e a sua localização. Fragmentos (ou segmentos) são os "pedaços" reais do conteúdo de vídeo.

### <a name="examples"></a>Exemplos

#### <a name="hls"></a>HLS

Aqui está um exemplo de um ficheiro manifesto HLS, também chamado de lista de reprodução principal do HLS: 

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

Aqui está um exemplo de um ficheiro manifesto MPEG-DASH, também chamado de Descrição de Apresentação de Mídia MPEG-DASH (MPD):

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

Aqui está um exemplo de um ficheiro manifesto smooth streaming:

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

### <a name="naming-of-tracks-in-the-manifest"></a>Nomeação de faixas no manifesto

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

### <a name="signaling-audio-description-tracks"></a>Sinalização de faixas de descrição áudio

Pode adicionar uma faixa de narração ao seu vídeo para ajudar os clientes com deficiência visual a seguir em vídeo a gravação ouvindo a narração. Precisa anotar uma faixa áudio como descrição áudio no manifesto. Para isso, adicione parâmetros de "acessibilidade" e "role" ao ficheiro .ism. É sua responsabilidade definir estes parâmetros corretamente para sinalizar uma faixa de áudio como descrição áudio. Por exemplo, adicione `<param name="accessibility" value="description" />` e `<param name="role" value="alternate"` ao ficheiro .ism para uma faixa de áudio específica. 

Para mais informações, consulte o Como sinalizar um exemplo descritivo da [faixa áudio.](signal-descriptive-audio-howto.md)

#### <a name="smooth-streaming-manifest"></a>Manifesto de streaming suave

Se estiver a reproduzir um stream de Streaming Suave, o manifesto transportará valores em `Accessibility` e `Role` atributos para essa faixa de áudio. Por exemplo, `Role="alternate" Accessibility="description"` seria adicionado no elemento `StreamIndex` para indicar que é uma descrição áudio.

#### <a name="dash-manifest"></a>Manifesto dash

Para o manifesto DASH, os dois elementos seguintes seriam adicionados para sinalizar a descrição áudio:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Lista de reprodução do HLS

Para hls v7 e acima `(format=m3u8-cmaf)`, a sua lista de reprodução transportaria `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` quando a faixa de descrição áudio for sinalizada.

#### <a name="example"></a>Exemplo

Para mais informações, consulte [Como sinalizar faixas de descrição áudio](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Para controlar o número de faixas, formatos, bitrates e janelas de tempo de apresentação que são enviadas aos jogadores, pode utilizar filtragem dinâmica com o pacote dinâmico dos Media Services. Para mais informações, consulte [os manifestos de pré-filtragem com o embalador dinâmico](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Encriptação dinâmica

Pode utilizar *encriptação dinâmica* para encriptar dinamicamente os seus conteúdos ao vivo ou a pedido com a AES-128 ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. A Media Services também presta um serviço para a entrega de chaves AES e licenças de DRM a clientes autorizados. Para mais informações, consulte [encriptação dinâmica](content-protection-overview.md).

> [!NOTE]
> A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="more-information"></a>Mais informações

Consulte a [comunidade azure media services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de apoio .

## <a name="next-steps"></a>Passos Seguintes

[Carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)

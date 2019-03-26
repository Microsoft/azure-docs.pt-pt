---
title: Descrição geral de empacotamento dinâmico dos serviços de multimédia do Azure | Documentos da Microsoft
description: O tópico apresenta uma visão geral do empacotamento dinâmico, nos serviços de multimédia.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 77cbc73c6c6aef40c482b0cfe456dcbd4b7e85d0
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435317"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

Serviços de multimédia do Microsoft Azure podem ser utilizados para disponibilizar formatos de muitos arquivos de origem do suporte de dados, suportes de dados em fluxo em formatos, e a proteção de conteúdo formata a uma variedade de tecnologias de cliente (por exemplo, iOS e XBOX). Estes clientes compreender protocolos diferentes, por exemplo iOS requer que um formato de HTTP Live Streaming (HLS) e o Xbox necessitam de transmissão em fluxo uniforme. Se tiver um conjunto de velocidade de transmissão adaptável (múltipla) MP4 ou um conjunto de arquivos transmissão em fluxo uniforme de velocidade de transmissão adaptável que deve servir para clientes que compreender HLS, MPEG DASH ou transmissão em fluxo uniforme de ficheiros (ISO Base Media 14496-12), pode tirar partido de dinâmico Empacotamento. O empacotamento é agnóstico para a resolução de vídeo, SD/HD/UHD - 4K são suportados.

[Pontos finais de transmissão em fluxo](streaming-endpoint-concept.md) é o serviço de empacotamento dinâmico dos serviços de multimédia utilizadas para disponibilizar conteúdo multimédia para jogadores do cliente. Empacotamento dinâmico é uma funcionalidade que vem padrão em todos os **pontos finais de transmissão em fluxo** (Standard ou Premium). 

Para tirar partido das **empacotamento dinâmico**, tem de ter um **Asset** com um conjunto de ficheiros MP4 de velocidade de transmissão adaptável e ficheiros de configuração de transmissão em fluxo necessários pelo empacotamento dinâmico de serviços de multimédia. É uma forma de obter os ficheiros codificar o (origem) mezanino com os Media Services. Para disponibilizar vídeos no elemento codificado para os clientes para a reprodução, tem de criar uma **localizador de transmissão em fluxo** e criar URLs de transmissão em fluxo. Em seguida, com base no formato especificado no manifesto cliente streaming (HLS, TRAÇO ou uniforme), receberá o fluxo no protocolo que escolheu.

Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente. 

Nos serviços de multimédia, o empacotamento dinâmico é utilizado se são transmissão em fluxo ao vivo ou sob demanda. 

## <a name="common-on-demand-workflow"></a>Fluxo de trabalho comum do sob demanda

Segue-se um comuns dos serviços de multimédia de transmissão em fluxo fluxo de trabalho em que é utilizado o empacotamento dinâmico.

1. Carregar um ficheiro de entrada (chamado de um ficheiro de mezanino). Por exemplo, MP4, MOV ou ficheiros do MXF (para obter a lista dos formatos suportados, consulte [formatos suportados pelo Media Encoder Standard](media-encoder-standard-formats.md).
2. Codificar o ficheiro de mezanino para os conjuntos H.264 MP4 de velocidade de transmissão adaptável.
3. Publique o elemento que contém a conjunto MP4 de velocidade de transmissão adaptável. Publicar através da criação de um **localizador de transmissão em fluxo**.
4. Crie os URLs que visam diferentes formatos (HLS, traço e transmissão em fluxo uniforme). O **ponto final de transmissão em fluxo** cuidar do que serve o manifesto correto e os pedidos para todos esses formatos diferentes.

O diagrama seguinte mostra o streaming sob demanda com o fluxo de trabalho de empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar em MP4s de velocidade de transmissão adaptável

Para obter informações sobre [como codificar um vídeo com os Media Services](encoding-concept.md), veja os exemplos seguintes:

* [Codificar a partir de um URL HTTPS com configurações predefinidas incorporadas](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando as configurações predefinidas incorporadas](job-input-from-local-file-how-to.md)
* [Criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino](customize-encoder-presets-how-to.md)

Para obter uma lista de formatos e codecs Media Encoder Standard, veja [formatos e codecs do](media-encoder-standard-formats.md)

## <a name="common-live-streaming-workflow"></a>Workflow comum de transmissão em fluxo em direto

Eis os passos para um fluxo de trabalho de transmissão em fluxo em direto:

1. Criar uma [evento em direto](live-events-outputs-concept.md).
1. Obtenha os URLs de ingestão e configurar seu codificador no local para utilizar o URL para enviar a contribuição do feed.
1. Obter o URL de pré-visualização e utilizá-lo para verificar que a entrada do codificador, na verdade, está a ser recebida.
1. Criar uma nova **Asset**.
1. Criar uma **Live saída** e utilize o nome de recurso que criou.<br/>O **saída Live** irá arquivar a transmissão para o **Asset**.
1. Criar uma **localizador de transmissão em fluxo** com o incorporado **política de transmissão em fluxo** tipos.<br/>Se pretende encriptar o seu conteúdo, consulte [descrição geral da proteção de conteúdo](content-protection-overview.md).
1. Lista os caminhos a **localizador de transmissão em fluxo** para recuperar os URLs para utilizar.
1. Obter o nome do anfitrião para o **ponto final de transmissão em fluxo** que deseja transmitir em fluxo do.
1. Crie os URLs que visam diferentes formatos (HLS, traço e transmissão em fluxo uniforme). O **ponto final de transmissão em fluxo** cuidar do que serve o manifesto correto e os pedidos para todos esses formatos diferentes.

Um evento em direto pode ser um dos dois tipos: codificação de pass-through e em direto. Para obter detalhes sobre a transmissão em fluxo em direto no Serviços de multimédia v3, consulte [descrição geral de transmissão em direto](live-streaming-overview.md).

O diagrama seguinte mostra a transmissão em direto com o fluxo de trabalho de empacotamento dinâmico.

![pass-through](./media/live-streaming/pass-through.svg)

## <a name="delivery-protocols"></a>Protocolos de entrega

|Protocolo|Exemplo|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Transmissão em Fluxo Uniforme| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Codecs de vídeo suportado pelo empacotamento dinâmico

Empacotamento dinâmico suporta ficheiros MP4, que contêm vídeo codificado com [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio suportado pelo empacotamento dinâmico

Suporta ficheiros MP4, que contêm áudio codificado com o empacotamento dinâmico [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(3 de AC avançada ou E AC3), Dolby Atmos, ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR de DTS, DTS HD, HD DTS sem perdas). Transmissão em fluxo de conteúdo de Dolby Atmos é suportada para padrões como o protocolo de MPEG-DASH com o formato de transmissão em fluxo comuns (CSF) ou o formato comum de aplicativo de suporte de dados (CMAF) real de MP4 fragmentado e através de HTTP Live Streaming (HLS) com CMAF.

> [!NOTE]
> Empacotamento dinâmico não suporta ficheiros que contêm [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) áudio (AC3) (é um codec herdado).

## <a name="dynamic-encryption"></a>Encriptação dinâmica

**A encriptação dinâmica** permite-lhe encriptar dinamicamente o seu conteúdo ao vivo ou sob demanda com AES-128 ou qualquer um dos sistemas de gestão (DRM) três direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. Para obter mais informações, consulte [encriptação dinâmica](content-protection-overview.md).

## <a name="manifests"></a>Manifestos 
 
Serviços de multimédia suportam HLS, MPEG DASH, Smooth Streaming protocolos. Como parte da **empacotamento dinâmico**, os transmissão em fluxo manifestos de cliente (lista de reprodução do mestre de HLS, descrição de apresentação de suporte de dados do TRAÇO (MPD) e transmissão em fluxo uniforme) são gerados dinamicamente com base no Seletor de formato no URL. Consulte os protocolos de entrega [esta secção](#delivery-protocols). 

Um arquivo de manifesto inclui a transmissão em fluxo de metadados, tais como: controlar o tipo (áudio, vídeo ou texto), controlar o nome, hora de início e fim, a velocidade de transmissão (qualidades), linguagens de controle, a janela de apresentação (janela deslizante de duração fixa), o codec de vídeo (FourCC). Também instrui o player para recuperar o fragmento seguinte, fornecendo informações sobre os seguintes pode ser reproduzidos vídeo fragmentos disponíveis e a respetiva localização. Fragmentos de (ou segmentos) são os reais "segmentos" de um conteúdo de vídeo.

### <a name="hls-master-playlist"></a>Lista de reprodução de mestre de HLS

Eis um exemplo de um ficheiro de manifesto HLS: 

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

### <a name="dash-media-presentation-description-mpd"></a>Descrição de apresentação de suporte de dados do TRAÇO (MPD)

Eis um exemplo de um manifesto de TRAÇO:

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
### <a name="smooth-streaming"></a>Transmissão em Fluxo Uniforme

Eis um exemplo de um manifesto de transmissão em fluxo uniforme:

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

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Filtragem dinâmica é utilizado para controlar o número de faixas, formatos, velocidades de transmissão e janelas de tempo de apresentação que são enviadas para os jogadores. Para obter mais informações, consulte [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).

> [!NOTE]
> Atualmente, não é possível utilizar o portal do Azure para gerir os recursos de v3. Utilize o [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), ou um suportadas [SDKs](developers-guide.md).

## <a name="next-steps"></a>Passos Seguintes

[Carregar, codificar, transmitir vídeos em fluxo](stream-files-tutorial-with-api.md)


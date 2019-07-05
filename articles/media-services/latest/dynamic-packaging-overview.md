---
title: Descrição geral de empacotamento dinâmico dos serviços de multimédia do Azure | Documentos da Microsoft
description: O artigo apresenta uma visão geral do empacotamento dinâmico, nos serviços de multimédia do Azure.
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
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 4836ec4bb66bbf8ced921dd1095665d004f8a28b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542580"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

Serviços de multimédia do Microsoft Azure podem ser utilizados para disponibilizar formatos de muitos arquivos de origem do suporte de dados, suportes de dados em fluxo em formatos, e a proteção de conteúdo formata a uma variedade de tecnologias de cliente (por exemplo, iOS e XBOX). Estes clientes compreender protocolos diferentes, por exemplo iOS requer que um formato de HTTP Live Streaming (HLS) e o Xbox necessitam de transmissão em fluxo uniforme. Se tiver um conjunto de velocidade de transmissão adaptável (múltipla) MP4 ou um conjunto de arquivos transmissão em fluxo uniforme de velocidade de transmissão adaptável que deve servir para clientes que compreender HLS, MPEG DASH ou transmissão em fluxo uniforme de ficheiros (ISO Base Media 14496-12), pode tirar partido de  *Empacotamento dinâmico*. O empacotamento é agnóstico para a resolução de vídeo, SD/HD/UHD - 4K são suportados.

Nos Media Services, um [ponto final de transmissão em fluxo](streaming-endpoint-concept.md) representa um dinâmico (just-in-time) empacotamento e a origem do serviço que pode distribuir os seus conteúdos em direto e a pedido diretamente a uma aplicação de leitor de cliente, utilizando um do comum de transmissão em fluxo protocolos de suporte de dados (HLS ou DASH). Empacotamento dinâmico é uma funcionalidade que vem padrão em todos os **pontos finais de transmissão em fluxo** (Standard ou Premium). 

Para tirar partido do empacotamento dinâmico, tem de ter uma **Asset** com um conjunto de ficheiros MP4 de velocidade de transmissão adaptável e transmissão em fluxo ficheiros de configuração necessários para um empacotamento dinâmico dos serviços de multimédia. Uma forma de obter os ficheiros é codificar o ficheiro mezzanine (de origem) com os Serviços de Multimédia. Para disponibilizar vídeos no elemento codificado para os clientes para a reprodução, tem de criar uma **localizador de transmissão em fluxo** e criar URLs de transmissão em fluxo. Em seguida, com base no formato especificado no manifesto cliente streaming (HLS, TRAÇO ou uniforme), receberá o fluxo no protocolo que escolheu.

Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente. 

Nos serviços de multimédia, o empacotamento dinâmico é utilizado se são transmissão em fluxo ao vivo ou sob demanda. 

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="on-demand-streaming-workflow"></a>Fluxo de trabalho de transmissão em fluxo a pedido

Este é um fluxo de trabalho comuns para serviços de multimédia a pedido de transmissão em fluxo com o empacotamento dinâmico:

1. Carregar um ficheiro de origem ou de entrada (chamado um *mezanino* ficheiro). Os exemplos incluem um ficheiro MP4, MOV ou ficheiros do MXF. 
1. Codificar o ficheiro de mezanino para os conjuntos H.264 MP4 de velocidade de transmissão adaptável. 
1. Publique o elemento que contém a conjunto MP4 de velocidade de transmissão adaptável. Publicar através da criação de um localizador de transmissão em fluxo.
1. Crie os URLs que visam diferentes formatos (HLS, MPEG-DASH e Smooth Streaming). O ponto final de transmissão em fluxo encarrega-se de que serve o manifesto correto e os pedidos para os diferentes formatos.

Este diagrama apresenta o fluxo de trabalho para a pedido de transmissão em fluxo com o empacotamento dinâmico:

![Diagrama de um fluxo de trabalho para a pedido de transmissão em fluxo com o empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de transmissão em fluxo em direto

Um evento em direto pode ser um dos dois tipos: codificação em direto ou pass-through. 

Este é um fluxo de trabalho comuns para a transmissão em fluxo em direto com o empacotamento dinâmico:

1. Criar uma [evento em direto](live-events-outputs-concept.md).
1. Obter o URL de ingestão e configurar o seu codificador no local para utilizar o URL para enviar a contribuição do feed.
1. Obter o URL de pré-visualização e utilizá-lo para verificar que a entrada do codificador está a ser recebida.
1. Crie um novo elemento.
1. Criar uma saída em direto e utilize o nome de recurso que criou.<br />A saída em direto arquiva o fluxo no elemento.
1. Crie um localizador de transmissão em fluxo com os tipos de política de transmissão em fluxo internos.<br />Se pretende encriptar o seu conteúdo, consulte [descrição geral da proteção de conteúdo](content-protection-overview.md).
1. Liste os caminhos no localizador de transmissão em fluxo para obter os URLs para utilizar.
1. Obtenha o nome de anfitrião para o ponto de final de transmissão em fluxo que pretende transmitir em fluxo do.
1. Crie os URLs que visam diferentes formatos (HLS, MPEG-DASH e Smooth Streaming). O ponto final de transmissão em fluxo encarrega-se de que serve o manifesto correto e os pedidos para os diferentes formatos.

Este diagrama apresenta o fluxo de trabalho para a transmissão em fluxo em direto com o empacotamento dinâmico:

![Diagrama de um fluxo de trabalho de codificação pass-through com o empacotamento dinâmico](./media/live-streaming/pass-through.svg)

Para obter informações sobre a transmissão em fluxo em direto no Serviços de multimédia v3, consulte [descrição geral de transmissão em direto](live-streaming-overview.md).

## <a name="delivery-protocols"></a>Protocolos de entrega

Pode usar esses protocolos de entrega para o seu conteúdo num empacotamento dinâmico dos serviços de multimédia:

|Protocol|Exemplo|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|CSF MPEG-DASH| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|CMAF MPEG-DASH|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Transmissão em Fluxo Uniforme| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar em MP4s de velocidade de transmissão adaptável

Os artigos seguintes mostram os exemplos de [como codificar um vídeo com os Media Services](encoding-concept.md):

* [Codificar a partir de um URL HTTPS, utilizando as configurações predefinidas incorporadas](job-input-from-http-how-to.md)
* [Codificar um ficheiro local utilizando as configurações predefinidas incorporadas](job-input-from-local-file-how-to.md)
* [Criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino](customize-encoder-presets-how-to.md)

Ver a lista de codificador de multimédia Standard [formatos e codecs](media-encoder-standard-formats.md).

## <a name="video-codecs"></a>Codecs de vídeo

Empacotamento dinâmico suporta os codecs de vídeo seguintes:
* Ficheiros MP4, que contêm o vídeo está codificado com [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1) ou [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1, ou hvc1).

## <a name="audio-codecs"></a>Codecs de áudio

Empacotamento dinâmico suporta os seguintes protocolos de áudio:
* Ficheiros MP4
* Várias faixas de áudio

Empacotamento dinâmico não suporta ficheiros que contêm [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) áudio (AC3) (é um codec herdado).

### <a name="mp4-files"></a>Ficheiros MP4

Empacotamento dinâmico suporta ficheiros MP4, que contêm o áudio é codificado com os seguintes protocolos: 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 ou v2 HE-AAC)
* [Dolby Digital mais](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (melhorado 3 de AC ou E AC3)
* Dolby Atmos<br />
   Transmissão em fluxo conteúdo de Dolby Atmos é suportada para padrões como o protocolo de MPEG-DASH com o formato de transmissão em fluxo comuns (CSF) ou o formato comum de aplicativo de suporte de dados (CMAF) real de MP4 fragmentado e através de HTTP Live Streaming (HLS) com CMAF.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Codecs DTS suportadas pelos formatos de empacotamento DASH-CSF, DASH CMAF, HLS M2TS e HLS CMAF são:  

    * Coloque Digital de DTS (dtsc)
    * DTS HD alta resolução e áudio de mestre de DTS HD (dtsh)
    * DTS Express (dtse)
    * DTS HD sem perdas (não existem núcleos) (dtsl)

### <a name="multiple-audio-tracks"></a>Várias faixas de áudio

Empacotamento dinâmico suporta múltiplas faixas de áudio para a saída HLS (versão 4 ou posterior) para transmissão em fluxo ativos, que têm várias faixas de áudio com vários codecs e linguagens.

## <a name="dynamic-encryption"></a>Encriptação dinâmica

Pode usar *encriptação dinâmica* para encriptar dinamicamente o seu conteúdo ao vivo ou sob demanda com AES-128 ou qualquer um dos três sistemas de gestão (DRM) de direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entregar licenças DRM e de chaves AES para os clientes autorizados. Para obter mais informações, consulte [encriptação dinâmica](content-protection-overview.md).

## <a name="manifest-examples"></a>Exemplos do manifesto 
 
Num empacotamento dinâmico dos serviços de multimédia, os manifestos dos clientes de transmissão em fluxo para HLS, MPEG-DASH e Smooth Streaming são gerados dinamicamente com base no Seletor de formato no URL. Para obter mais informações, consulte [protocolos de entrega](#delivery-protocols). 

Um arquivo de manifesto inclui a transmissão em fluxo de metadados, como o tipo de controle (áudio, vídeo ou texto), controlar o nome, início e hora de fim, a velocidade de transmissão (qualidades), linguagens de controle, janela de apresentação (janela de duração fixa de deslizante) e o codec de vídeo (FourCC). Também instrui o player para recuperar o fragmento seguinte, fornecendo informações sobre os seguintes fragmentos de vídeo pode ser reproduzidos que estão disponíveis e a respetiva localização. Fragmentos de (ou segmentos) são os "segmentos" reais de conteúdo de vídeo.

### <a name="hls"></a>HLS

Eis um exemplo de um arquivo de manifesto HLS, também chamado de uma lista de reprodução de mestre de HLS: 

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

### <a name="mpeg-dash"></a>MPEG-DASH

Eis um exemplo de um arquivo de manifesto do MPEG-DASH, também chamado de uma descrição de apresentação de suporte de dados de MPEG-DASH (MPD):

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

Eis um exemplo de um arquivo de manifesto de transmissão em fluxo uniforme:

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

Para controlar o número de faixas, formatos, velocidades de transmissão e janelas de tempo de apresentação que são enviadas para jogadores, pode utilizar a filtragem dinâmica com o empacotador de dinâmico de serviços de multimédia. Para obter mais informações, consulte [previamente filtragem se manifesta com o empacotador de dinâmico](filters-dynamic-manifest-overview.md).

## <a name="more-information"></a>Mais informações

Confira [Comunidade dos serviços de multimédia do Azure](media-services-community.md) para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md).


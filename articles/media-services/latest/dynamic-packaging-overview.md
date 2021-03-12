---
title: Embalagem dinâmica em Azure Media Services v3
description: Este artigo apresenta uma visão geral da embalagem dinâmica na Azure Media Services.
author: myoungerman
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 4f4f53d4a20397f38b565cb73e74b01d15cc3022
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633058"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Embalagem dinâmica em Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O Microsoft Azure Media Services pode ser utilizado para codificar muitos formatos de ficheiros de origem de mídia. Fornece-os através de diferentes protocolos de streaming, com ou sem proteção de conteúdos, para chegar a todos os principais dispositivos (como dispositivos iOS e Android). Estes clientes entendem diferentes protocolos. Por exemplo, o iOS requer que os streams sejam entregues em formato HTTP Live Streaming (HLS) e dispositivos Android suportam HLS, bem como MPEG DASH.

Nos Media Services, um [ponto final de streaming](streaming-endpoint-concept.md) (origem) representa um serviço dinâmico (just-in-time) de embalagem e origem que pode entregar o seu conteúdo ao vivo e a pedido diretamente a uma aplicação de cliente. Utiliza um dos protocolos comuns de streaming mencionados na secção seguinte. *A embalagem dinâmica* é uma característica que vem de série em todos os pontos finais de streaming.

## <a name="to-prepare-your-source-files-for-delivery"></a>Para preparar os seus ficheiros de origem para entrega

Para tirar partido da embalagem dinâmica, é necessário [codificar](encoding-concept.md) o seu ficheiro mezanino (fonte) num conjunto de ficheiros MP4 (ISO Base Media 14496-12). É necessário ter um [ativo](assets-concept.md) com os ficheiros de configuração DE MP4 codificados e de streaming necessários pela embalagem dinâmica dos Media Services. A partir deste conjunto de ficheiros MP4, pode utilizar embalagens dinâmicas para entregar vídeo através dos protocolos de streaming de meios descritos abaixo.

A embalagem dinâmica Azure Media Services suporta apenas ficheiros de vídeo e áudio no formato de contentorES MP4. Os ficheiros de áudio devem ser codificados num recipiente MP4 quando utilizarem códigos alternativos como o Dolby.  

> [!TIP]
> Uma forma de obter os ficheiros de configuração MP4 e streaming é [codificar o seu ficheiro mezanino com os Media Services](#encode-to-adaptive-bitrate-mp4s). 

Para disponibilizar vídeos no ativo codificado aos clientes para reprodução, tem de criar um [Localizador de Streaming](streaming-locators-concept.md) e construir URLs de streaming. Em seguida, com base no formato especificado no manifesto do cliente de streaming (HLS, MPEG DASH ou Smooth Streaming), recebe o stream no protocolo que escolheu.

Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

Se planeia proteger o seu conteúdo utilizando encriptação dinâmica dos Media Services, consulte [os protocolos de streaming e os tipos de encriptação](content-protection-overview.md#streaming-protocols-and-encryption-types).

### <a name="hls-protocol"></a>Protocolo HLS

O seu cliente de streaming pode especificar os seguintes formatos HLS:

|Protocolo|Exemplo|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

> [!NOTE]
> As diretrizes anteriores da Apple recomendaram que o recuo para redes de largura de banda baixa fosse fornecer um fluxo apenas de áudio.  Atualmente, o codificadora media services gera automaticamente uma faixa apenas áudio.  As diretrizes da Apple afirmam agora que a faixa apenas para áudio *não* deve ser incluída, especialmente para a distribuição da Apple TV.  Para evitar que o leitor falhe numa faixa apenas de áudio, sugerimos a utilização da etiqueta "audio-only=falso" no URL que remove a rendição apenas áudio no HLS ou simplesmente utilizar o HLS-V3. Por exemplo, `http://host/locator/asset.ism/manifest(format=m3u8-aapl,audio-only=false)`.

### <a name="mpeg-dash-protocol"></a>Protocolo MPEG-DASH

O seu cliente de streaming pode especificar os seguintes formatos MPEG-DASH:

|Protocolo|Exemplo|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Protocolo de streaming suave

O seu cliente de streaming pode especificar os seguintes formatos de Streaming Suave:

|Protocolo|Notas/exemplos| 
|---|---|
|Transmissão em Fluxo Uniforme| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Streaming Suave 2.0 (manifesto legado)|Por predefinição, o formato manifesto de streaming suave contém a etiqueta de repetição (r-tag). No entanto, alguns jogadores não apoiam o `r-tag` . Os clientes com estes jogadores podem utilizar um formato que desativa a marca r:00:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> O Smooth Streaming requer que tanto o áudio como o vídeo estejam presentes no seu stream.

## <a name="on-demand-streaming-workflow"></a>Fluxo de trabalho de streaming a pedido

Os passos seguintes mostram um fluxo de trabalho comum de streaming de Serviços de Media onde a embalagem dinâmica é utilizada juntamente com o Standard Encoder em Azure Media Services.

1. [Faça o upload de um ficheiro](job-input-from-http-how-to.md) de entrada como um MP4, QuickTime/MOV ou outro formato de ficheiro suportado. Este ficheiro também é referido como o mezanino ou o ficheiro de origem. Para a lista de formatos suportados, consulte [Formatos Suportados pelo Codificadores Standard](media-encoder-standard-formats.md).
1. [Codificar](#encode-to-adaptive-bitrate-mp4s) o seu ficheiro mezanino num conjunto de bitrate adaptativo H.264/AAC MP4.

    Se já tiver ficheiros codificados e apenas quiser copiar e transmitir os ficheiros, utilize: [CopyVideo](/rest/api/media/transforms/createorupdate#copyvideo) e [CopyAudio](/rest/api/media/transforms/createorupdate#copyaudio) APIs. Um novo ficheiro MP4 com um manifesto de streaming (.ficheiro ism) será criado como resultado.
1. Publique o ativo de saída que contém o conjunto de BITRATE adaptativo MP4. Publica criando um [localizador de streaming.](streaming-locators-concept.md)
1. Construa URLs que visam diferentes formatos (HLS, MPEG-DASH e Smooth Streaming). O *ponto final de streaming* cuidaria de servir o manifesto correto e os pedidos para todos estes formatos diferentes.
    
O diagrama a seguir mostra o streaming a pedido com fluxo de trabalho dinâmico de embalagem.

![Diagrama de um fluxo de trabalho para streaming a pedido com embalagem dinâmica](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

O caminho de descarregamento está presente na imagem acima apenas para mostrar que pode descarregar um ficheiro MP4 diretamente através do *ponto final de streaming* (origem) (especifica a política de [streaming](streaming-policy-concept.md) descarregada no localizador de streaming).<br/>O embalador dinâmico não está a alterar o ficheiro. Pode utilizar opcionalmente as APIs de armazenamento de blob Azure para aceder a um MP4 diretamente para download progressivo se desejar contornar as funcionalidades *de ponta de streaming* (origem). 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar para adaptáveis bitrate MP4s

Os seguintes artigos mostram exemplos de [como codificar um vídeo com os Media Services:](encoding-concept.md)

* [Codificar a partir de um URL HTTPS utilizando predefinições incorporadas](job-input-from-http-how-to.md).
* [Codificar um ficheiro local utilizando predefinições incorporadas](job-input-from-local-file-how-to.md).
* [Construa uma predefinição personalizada para direcionar o seu cenário específico ou requisitos do dispositivo](customize-encoder-presets-how-to.md).

Consulte a lista de [formatos e códigos](media-encoder-standard-formats.md)Standard Encoder .

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Um evento ao vivo pode ser definido para um *passe-through* (um codificadora ao vivo no local envia um fluxo bitrate múltiplo) ou *codificação ao vivo* (um codificante ao vivo no local envia um único fluxo de bitrate). 

Aqui está um fluxo de trabalho comum para streaming ao vivo com *embalagem dinâmica:*

1. Criar um [evento ao vivo.](live-events-outputs-concept.md)
1. Obtenha o URL inger e configuure o seu codificadora no local para usar o URL para enviar o feed de contribuição.
1. Obtenha o URL de pré-visualização e use-o para verificar se a entrada do codificante está a ser recebida.
1. Criar um novo ativo.
1. Crie uma saída ao vivo e use o nome de ativo que criou.<br />A saída ao vivo arquiva o fluxo para o ativo.
1. Crie um localizador de streaming com os tipos de política de streaming incorporados.<br />Se pretende encriptar o seu conteúdo, reveja a [visão geral da proteção de conteúdos.](content-protection-overview.md)
1. Enuse os caminhos no localizador de streaming para obter os URLs para usar.
1. Obtenha o nome de anfitrião para o ponto final de streaming a partir do que pretende transmitir.
1. Construa URLs que visam diferentes formatos (HLS, MPEG-DASH e Smooth Streaming). O *ponto final* de streaming cuida de servir o manifesto correto e os pedidos para os diferentes formatos.

Este diagrama mostra o fluxo de trabalho para streaming ao vivo com *embalagem dinâmica:*

![Diagrama de um fluxo de trabalho para codificação pass-through com embalagem dinâmica](./media/live-streaming/pass-through.svg)

Para obter informações sobre o streaming em direto nos Media Services v3, consulte [a visão geral do streaming ao vivo](live-streaming-overview.md).

## <a name="video-codecs-supported-by-dynamic-packaging"></a>Códigos de vídeo suportados por Dynamic Packaging

A embalagem dinâmica suporta ficheiros de vídeo que se encontram no formato de ficheiro de contentores MP4 e contêm vídeo codificado com [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC ou AVC1) ou [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 ou hvc1).

> [!NOTE]
> Foram testadas resoluções de até 4K e taxas de fotogramas até 60 fotogramas/segundo com *embalagens dinâmicas.*

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio suportados por embalagens dinâmicas

A embalagem dinâmica também suporta ficheiros áudio que são armazenados no formato de contentor de ficheiros MP4 contendo fluxo de áudio codificado num dos seguintes códigos:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, ou HE-AAC v2). 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (AC-3 ou E-AC3 melhorados).  O áudio codificado deve ser armazenado no formato de recipiente MP4 para funcionar com a Dynamic Packaging.
* Dolby Atmos

   O conteúdo do Streaming Dolby Atmos é suportado para padrões como o protocolo MPEG-DASH com o Formato Comum de Streaming (CSF) ou o Formato Comum de Aplicação de Mídia (CMAF) fragmentado MP4, e via HTTP Live Streaming (HLS) com CMAF.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   Os códigos DTS suportados pelos formatos de embalagem DASH-CSF, DASH-CMAF, HLS-M2TS e HLS-CMAF são:  

    * DTS Digital Surround (dtsc)
    * DTS-HD Alta Resolução e DTS-HD Master Audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Sem perdas (sem núcleo) (dtsl)

A embalagem dinâmica suporta múltiplas faixas de áudio com DASH ou HLS (versão 4 ou posterior) para streaming de ativos que têm múltiplas faixas de áudio com vários codecs e idiomas.

Para todos os códigos de áudio acima, o áudio codificado deve ser armazenado no formato de recipiente MP4 para funcionar com a Dynamic Packaging. O serviço não suporta formatos de ficheiros de fluxo elementar brutos no armazenamento de bolhas (por exemplo, o seguinte não seria suportado - .dts, .ac3.) 

Apenas os ficheiros com .mp4 de extensão .mp4a são suportados para embalagem áudio. 

### <a name="limitations"></a>Limitações

#### <a name="ios-limitation-on-aac-51-audio"></a>Limitação do iOS no áudio AAC 5.1

Os dispositivos apple iOS não suportam 5.1 código sonoro AAC. O áudio multicanal deve ser codificado utilizando codecs Dolby Digital ou Dolby Digital Plus.

Para obter informações detalhadas, consulte [a especificação de autoria do HLS para dispositivos apple](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices).

> [!NOTE]
> Os Media Services não suportam a codificação de dolby Digital, Dolby Digital Plus ou Dolby Digital Plus com formatos de áudio multicanal Dolby Atmos.

#### <a name="dolby-digital-audio"></a>Áudio digital Dolby

Atualmente, a embalagem dinâmica dos Media Services não suporta ficheiros que contenham áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (uma vez que este é considerado um código legado por Dolby).

## <a name="manifests"></a>Manifestos

Nas *embalagens dinâmicas* dos Media Services, os manifestos do cliente de streaming para HLS, MPEG-DASH e Smooth Streaming são gerados dinamicamente com base no seletor de formato no URL.  

Um ficheiro manifesto inclui metadados de streaming como tipo de faixa (áudio, vídeo ou texto), nome da faixa, tempo de início e fim, bitrate (qualidades), linguagens de faixa, janela de apresentação (janela deslizante de duração fixa) e código de vídeo (FourCC). Também instrui o jogador a recuperar o próximo fragmento, fornecendo informações sobre os próximos fragmentos de vídeo jogáveis que estão disponíveis e a sua localização. Fragmentos (ou segmentos) são os verdadeiros "pedaços" de conteúdo de vídeo.

### <a name="examples"></a>Exemplos

#### <a name="hls"></a>HLS

Aqui está um exemplo de um ficheiro manifesto HLS, também chamado de lista de reprodução principal HLS: 

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

Aqui está um exemplo de um ficheiro manifesto MPEG-DASH, também chamado de MPEG-DASH Media Presentation Description (MPD):

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

Aqui está um exemplo de um ficheiro manifesto de streaming suave:

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

Se um nome de faixa áudio for especificado no ficheiro .ism, os Serviços de Comunicação adicionam um `Label` elemento dentro de um para `AdaptationSet` especificar as informações texturais para a faixa de áudio específica. Um exemplo do manifesto dash de saída:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

O leitor pode utilizar o `Label` elemento para visualizar a sua UI.

### <a name="signaling-audio-description-tracks"></a>Faixas de descrição áudio de sinalização

Pode adicionar uma faixa de narração ao seu vídeo para ajudar os clientes com deficiência visual a seguirem a gravação de vídeo ouvindo a narração. É necessário anotar uma faixa áudio como descrição áudio no manifesto. Para isso, adicione os parâmetros de "acessibilidade" e "função" ao ficheiro .ism. É sua responsabilidade definir estes parâmetros corretamente para sinalizar uma faixa áudio como descrição áudio. Por exemplo, adicione `<param name="accessibility" value="description" />` e `<param name="role" value="alternate"` ao ficheiro .ism para uma faixa áudio específica. 

Para obter mais informações, consulte o Como sinalizar um exemplo [de faixa sonora descritiva.](signal-descriptive-audio-howto.md)

#### <a name="smooth-streaming-manifest"></a>Manifesto de streaming suave

Se estiver a reproduzir um stream de Streaming Suave, o manifesto transportaria valores `Accessibility` e `Role` atributos para essa faixa de áudio. Por exemplo, `Role="alternate" Accessibility="description"` seria adicionado no elemento para indicar que é uma `StreamIndex` descrição áudio.

#### <a name="dash-manifest"></a>Manifesto do DASH

Para o manifesto dash, os dois elementos seguintes seriam adicionados para sinalizar a descrição áudio:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>Lista de reprodução de HLS

Para o HLS v7 e `(format=m3u8-cmaf)` acima, a sua lista de reprodução seria transportado `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` quando a faixa de descrição áudio estiver sinalizada.

#### <a name="example"></a>Exemplo

Para obter mais informações, consulte [como sinalizar faixas de descrição áudio](signal-descriptive-audio-howto.md).

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Para controlar o número de faixas, formatos, bitrates e janelas de tempo de apresentação que são enviadas aos jogadores, pode utilizar a filtragem dinâmica com o pacote dinâmico dos Media Services. Para obter mais informações, consulte [os manifestos de pré-filtragem com o embalador dinâmico](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Encriptação dinâmica

Pode utilizar *encriptação dinâmica* para encriptar dinamicamente o seu conteúdo ao vivo ou a pedido com a AES-128 ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Serviços de Comunicação Social também fornecem um serviço de entrega de chaves AES e licenças de DRM a clientes autorizados. Para obter mais informações, consulte [a encriptação dinâmica.](content-protection-overview.md)

> [!NOTE]
> Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="more-information"></a>Mais informações

Consulte a [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

[Carregar, codificar e transmitir vídeos](stream-files-tutorial-with-api.md)
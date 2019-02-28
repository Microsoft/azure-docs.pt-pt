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
ms.date: 02/26/2019
ms.author: juliako
ms.openlocfilehash: cd4eacc918acdf50bb256077030b86e121f663f0
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985806"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

Serviços de multimédia do Microsoft Azure podem ser utilizados para disponibilizar formatos de muitos arquivos de origem do suporte de dados, suportes de dados em fluxo em formatos, e a proteção de conteúdo formata a uma variedade de tecnologias de cliente (por exemplo, iOS e XBOX). Estes clientes compreender protocolos diferentes, por exemplo iOS requer que um formato de HTTP Live Streaming (HLS) e o Xbox necessitam de transmissão em fluxo uniforme. Se tiver um conjunto de velocidade de transmissão adaptável (múltipla) MP4 ou um conjunto de arquivos transmissão em fluxo uniforme de velocidade de transmissão adaptável que deve servir para clientes que compreender HLS, MPEG DASH ou transmissão em fluxo uniforme de ficheiros (ISO Base Media 14496-12), pode tirar partido de dinâmico Empacotamento. O empacotamento é agnóstico para a resolução de vídeo, SD/HD/UHD - 4K são suportados.

[Pontos finais de transmissão em fluxo](streaming-endpoint-concept.md) é o serviço de empacotamento dinâmico dos serviços de multimédia utilizadas para disponibilizar conteúdo multimédia para jogadores do cliente. Empacotamento dinâmico é uma funcionalidade que vem padrão em todos os **pontos finais de transmissão em fluxo** (Standard ou Premium). Não existe nenhum extra custo associado esta funcionalidade em serviços de multimédia v3. 

Para tirar partido das **empacotamento dinâmico**, tem de ter um **Asset** com um conjunto de ficheiros MP4 de velocidade de transmissão adaptável e ficheiros de manifestos. É uma forma de obter os ficheiros codificar o (origem) mezanino com os Media Services. Para que os vídeos no recurso de (com MP4s codificado e manifestos de servidor e cliente) disponível para os clientes para a reprodução, tem de criar uma **localizador de transmissão em fluxo** e, em seguida, crie URLs de transmissão em fluxo. Em seguida, com base no formato especificado no manifesto do cliente, recebe o fluxo no protocolo que escolheu.

Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente. 

Nos serviços de multimédia, o empacotamento dinâmico é utilizado se são transmissão em fluxo ao vivo ou sob demanda. O diagrama seguinte mostra o streaming sob demanda com o fluxo de trabalho de empacotamento dinâmico.

![Codificação dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Vídeo a pedido fluxo de trabalho comum

Segue-se um comuns dos serviços de multimédia de transmissão em fluxo fluxo de trabalho em que é utilizado o empacotamento dinâmico.

1. Carregar um ficheiro de entrada (chamado de um ficheiro de mezanino). Por exemplo, H.264, MP4 ou WMV (para obter a lista dos formatos suportados, consulte [formatos suportados pelo Media Encoder Standard](media-encoder-standard-formats.md).
2. Codificar o ficheiro de mezanino para os conjuntos H.264 MP4 de velocidade de transmissão adaptável.
3. Publique o elemento que contém a conjunto MP4 de velocidade de transmissão adaptável. Publicar através da criação de um **localizador de transmissão em fluxo**.
4. Crie os URLs que visam diferentes formatos (HLS, traço e transmissão em fluxo uniforme). O **ponto final de transmissão em fluxo** cuidar do que serve o manifesto correto e os pedidos para todos esses formatos diferentes.

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Codificar em MP4s de velocidade de transmissão adaptável

Para obter informações sobre [como codificar um vídeo com os Media Services](encoding-concept.md), veja os exemplos seguintes:

* [Codificar a partir de um URL HTTPS com configurações predefinidas incorporadas](job-input-from-http-how-to.md)
* [Codificar um arquivo local usando as configurações predefinidas incorporadas](job-input-from-local-file-how-to.md)
* [Criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino](customize-encoder-presets-how-to.md)

Para obter uma lista de formatos e codecs Media Encoder Standard, veja [formatos e codecs do](media-encoder-standard-formats.md)

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

Suporta ficheiros MP4, que contêm áudio codificado com o empacotamento dinâmico [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (3 de AC avançada ou E AC3), ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR de DTS, DTS HD, HD DTS sem perdas).

> [!NOTE]
> Empacotamento dinâmico não suporta ficheiros que contêm [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) áudio (AC3) (é um codec herdado).

## <a name="manifest-files-overview"></a>Descrição geral de ficheiros de manifesto

R **manifesto** (lista de reprodução) ficheiro (baseado em texto ou baseado em XML) inclui a transmissão em fluxo de metadados, tais como: controlar o tipo (áudio, vídeo ou texto), controlar o nome, hora de início e fim, a velocidade de transmissão (qualidades), linguagens de controle, janela de apresentação (deslizante codec de vídeo de janela de duração fixa), (FourCC). Também instrui o player para recuperar o fragmento seguinte, fornecendo informações sobre os seguintes pode ser reproduzidos vídeo fragmentos disponíveis e a respetiva localização. Fragmentos de (ou segmentos) são os reais "segmentos" de um conteúdo de vídeo.

Eis um exemplo de um ficheiro de manifesto HLS: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

## <a name="next-steps"></a>Passos Seguintes

[Carregar, codificar, transmitir vídeos em fluxo](stream-files-tutorial-with-api.md)


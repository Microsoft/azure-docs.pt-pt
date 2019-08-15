---
title: Formatos e codecs de Media Encoder Standard – Azure
description: Este tópico fornece uma visão geral dos codecs e formatos de Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 8fd68269f73506c2d605c0cb4560e6a57e1a2128
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016528"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Formatos e Codecs do Codificador de Multimédia Standard

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 2](media-services-media-encoder-standard-formats.md)
> * [Versão 3](../latest/media-encoder-standard-formats.md)

Este documento contém uma lista dos formatos de arquivo de importação e exportação mais comuns que você pode usar com Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Formatos de contêiner/arquivo de entrada
| Formatos de arquivo (extensões de arquivo) | Suportadas |
| --- | --- |
| FLV (com codecs H. 264 e AAC) (. flv) |Sim |
| MXF    (.mxf) |Sim |
| GXF (. GXF) |Sim |
| MPEG2-PS, MPEG2-TS, 3GP (. TS,. PS,. 3GP,. 3GPP,. mpg) |Sim |
| Vídeo do Windows Media (WMV)/ASF (. wmv,. ASF) |Sim |
| AVI (8 bits/descompactado não compactados) (. avi) |Sim |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Sim |
| [Gravação de vídeo digital da Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (. DVR-MS) |Sim |
| Matroska/WebM (.mkv) |Sim |
| WAVE/WAV (. wav) |Sim |
| QuickTime (. mov) |Sim |

> [!NOTE]
> Acima está uma lista das extensões de arquivo mais comumente encontradas. Media Encoder Standard dá suporte a muitos outros (por exemplo:. M2TS,. mpeg2video,. QT). Se você tentar codificar um arquivo e receber uma mensagem de erro sobre o formato sem suporte, forneça seus comentários [aqui](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formatos de áudio em contêineres de entrada
O Media Encoder Standard dá suporte ao transporte dos seguintes formatos de áudio em contêineres de entrada:

* Arquivos MXF, GXF e QuickTime, que têm faixas de áudio com amostras estéreo intercaladas ou 5,1

ou

* Arquivos MXF, GXF e QuickTime onde o áudio é transportado como faixas de PCM separadas, mas o mapeamento de canal (para estéreo ou 5,1) pode ser deduzido dos metadados do arquivo

## <a name="input-video-codecs"></a>Codecs de vídeo de entrada
| Codecs de vídeo de entrada | Suportadas |
| --- | --- |
| AVC de 8 bits/10 bits, até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |
| Ávido DNxHD (em MXF) |Sim |
| DVCPro/DVCProHD (em MXF) |Sim |
| Vídeo digital (DV) (em arquivos AVI) |Sim |
| JPEG 2000 |Sim |
| MPEG-2 (até 422 de perfil e alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até 422 perfil |
| MPEG-1 |Sim |
| VC-1/WMV9 |Sim |
| Canopus HQ/HQX |Não |
| MPEG-4 parte 2 |Sim |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |
| YUV420 não compactado ou mezanino |Sim |
| Apple ProRes 422 |Sim |
| Apple ProRes 422 LT |Sim |
| Apple ProRes 422 HQ |Sim |
| Proxy Apple ProRes |Sim |
| Apple ProRes 4444 |Sim |
| Apple ProRes 4444 XQ |Sim |
| HEVC/H. 265| Perfis de 10 (&#42;) principal e principal<br/>O principal suporte a perfis 10 destina-se ao conteúdo do 8 bits 4:2:0. |

## <a name="input-audio-codecs"></a>Codecs de áudio de entrada
| Codecs de áudio de entrada | Suportadas |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5,1) |Sim |
| Camada 2 MPEG |Sim |
| MP3 (MPEG-1 áudio camada 3) |Sim |
| Áudio do Windows Media |Sim |
| WAV/PCM |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Sim |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |
| AMR (várias taxas adaptáveis) |Sim |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |
| Dolby® E |Não |
| Dolby® Digital (AC3) |Não |
| Dolby® Digital Plus (E-AC3) |Não |

## <a name="output-formats-and-codecs"></a>Formatos de saída e codecs
A tabela a seguir lista os codecs e formatos de arquivo com suporte para exportação.

| Formato do Ficheiro | Codec de vídeo | Codec de áudio |
| --- | --- | --- |
| MP4 <br/><br/>(incluindo contêineres MP4 de múltiplas taxas de bits) |H. 264 (perfis altos, principais e de linha de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H. 264 (perfis altos, principais e de linha de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Codificando conteúdo sob demanda com os serviços de mídia do Azure](media-services-encode-asset.md)

[Como codificar com Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)


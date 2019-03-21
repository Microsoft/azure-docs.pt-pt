---
title: Codificador de multimédia Standard formatos e codecs - Azure
description: Este tópico apresenta uma visão geral do Media Encoder Standard formatos e codecs.
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
ms.date: 02/10/2019
ms.author: juliako;anilmur
ms.openlocfilehash: 4c7e0e091aeecb205c63b8fa7e97d7592f15b856
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58084383"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Formatos e codecs Media Encoder Standard

Este artigo contém uma lista do importar mais comuns e formatos de ficheiro de exportação que pode utilizar com o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Para obter informações sobre como criar configurações predefinidas personalizadas usando **StandardEncoderPreset**, consulte [criar uma transformação com uma configuração predefinida personalizada](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Formatos de arquivo/contentor de entrada

| Formatos de ficheiro (extensões de ficheiro) | Suportadas |
| --- | --- |
| FLV (com codecs H.264 e AAC) (. flv) |Sim |
| MXF    (.mxf) |Sim |
| GXF    (.gxf) |Sim |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Sim |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Sim |
| AVI (descomprimido 8 bits/10 bits) (. avi) |Sim |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Sim |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Sim |
| Matroska/WebM (.mkv) |Sim |
| WAVE/WAV (. wav) |Sim |
| QuickTime (.mov) |Sim |

> [!NOTE]
> Acima encontra-se uma lista das extensões de ficheiro encontradas mais frequentemente. Codificador de multimédia Standard suporta muitas outras (por exemplo:. m2ts,. mpeg2video,. qt). Se tentar codificar um ficheiro e receber uma mensagem de erro sobre o formato não suportado, fornecer seus comentários [aqui](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formatos de áudio em contentores de entrada
Codificador de multimédia Standard suporta os seguintes formatos de áudio em contentores de entrada:

* Arquivos de ficheiros do MXF, do GXF e do QuickTime que têm faixas de áudio com estéreo intercalado ou 5.1 amostras

ou

* Arquivos de ficheiros do MXF, do GXF e do QuickTime onde o áudio é executado como faixas PCM em separado, mas o mapeamento do canal (para estéreo ou 5.1) pode ser reduzido a partir dos metadados

## <a name="input-video-codecs"></a>Codecs de vídeo de entrada
| Codecs de vídeo de entrada | Suportadas |
| --- | --- |
| AVC 8 bits/10-bits, até 4:2:2, incluindo AVCIntra |4 de 8 bits: 2:0 e 4:2:2 |
| Avid DNxHD (no MXF) |Sim |
| DVCPro/DVCProHD (no MXF) |Sim |
| Vídeo digital (DV) (em ficheiros AVI) |Sim |
| JPEG 2000 |Sim |
| MPEG-2 (até perfil 422 e alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até perfil 422 |
| MPEG-1 |Sim |
| VC-1/WMV9 |Sim |
| Canopus HQ/HQX |Não |
| MPEG-4 parte 2 |Sim |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |
| YUV420 descomprimido ou mezanino |Sim |
| Apple ProRes 422 |Sim |
| Apple ProRes 422 LT |Sim |
| Apple ProRes 422 HQ |Sim |
| Apple ProRes Proxy |Sim |
| Apple ProRes 4444 |Sim |
| Apple ProRes 4444 XQ |Sim |
| HEVC/H.265| Perfil de principal|

## <a name="input-audio-codecs"></a>Codecs de áudio de entrada
| Codecs de áudio de entrada | Suportadas |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |
| MPEG camada 2 |Sim |
| MP3 (MPEG-1 camada de áudio 3) |Sim |
| Windows Media Audio |Sim |
| WAV/PCM |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Sim |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |
| AMR (taxa multi adaptável) |Sim |
| AES (SMPTE 331m e 302 M, AES3 2003) |Não |
| Dolby® E |Não |
| Dolby® Digital (AC3) |Não |
| Dolby® Digital Plus (E-AC3) |Não |

## <a name="output-formats-and-codecs"></a>E codecs de formatos de saída
A tabela seguinte lista os codecs e formatos de arquivo que são suportados para exportação.

| Formato do Ficheiro | Codec de vídeo | Codec de áudio |
| --- | --- | --- |
| MP4 <br/><br/>(incluindo contentores de MP4 de velocidade de transmissão) |H.264 (alta, Main e perfis de linha de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (alta, Main e perfis de linha de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Passos Seguintes

[Criar uma transformação com uma configuração predefinida personalizada](customize-encoder-presets-how-to.md)

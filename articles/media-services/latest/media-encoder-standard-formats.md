---
title: Formatos e códigos Standard Encoder - Azure
description: Este artigo contém uma lista dos formatos de ficheiros de importação e exportação mais comuns que pode utilizar com o StandardEncoderPreset.
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
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251365"
---
# <a name="standard-encoder-formats-and-codecs"></a>Formatos e codecs Standard Encoder

Este artigo contém uma lista dos formatos de ficheiros de importação e exportação mais comuns que pode utilizar com [standardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Para obter informações sobre como criar predefinições personalizadas utilizando **o StandardEncoderPreset,** consulte [Criar uma transformação com um preset personalizado](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Formatos de recipiente de entrada/ficheiro

| Formatos de ficheiros (extensões de ficheiros) | Suportado |
| --- | --- |
| FLV (com códices H.264 e AAC) (.flv) |Sim |
| MXF    (.mxf) |Sim |
| GXF (.gxf) |Sim |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Sim |
| Windows Media Video (WMV)/ASF (.wmv, .asf) |Sim |
| AVI (Não comprimido 8bit/10bit) (.avi) |Sim |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Sim |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Sim |
| Matroska/WebM (.mkv) |Sim |
| WAVE/WAV (.wav) |Sim |
| QuickTime (.mov) |Sim |

### <a name="audio-formats-in-input-containers"></a>Formatos de áudio em recipientes de entrada

O Encoder Standard suporta o transporte dos seguintes formatos áudio em recipientes de entrada:

* Ficheiros MXF, GXF e QuickTime, que têm faixas áudio com estéreo intercalada ou 5.1 amostras

ou

* Ficheiros MXF, GXF e QuickTime onde o áudio é transportado como faixas pcm separadas, mas o mapeamento do canal (para estéreo ou 5.1) pode ser deduzido dos metadados do ficheiro

## <a name="input-video-codecs"></a>Input video codecs
| Input video codecs | Suportado |
| --- | --- |
| AVC 8-bit/10-bit, até 4:2:2, incluindo AVCIntra |8 bit4:2:0 e 4:2:2 |
| DNxHD ávido (em MXF) |Sim |
| DVCPro/DVCProHD (em MXF) |Sim |
| Vídeo digital (DV) (em ficheiros AVI) |Sim |
| JPEG 2000 |Sim |
| MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até 422 Perfil |
| MPEG-1 |Sim |
| VC-1/WMV9 |Sim |
| Canopus HQ/HQX |Não |
| MPEG-4 Parte 2 |Sim |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |
| YUV420 descomprimido, ou mezanino |Sim |
| Apple ProRes 422 |Sim |
| Apple ProRes 422 LT |Sim |
| Apple ProRes 422 HQ |Sim |
| Procuração Apple ProRes |Sim |
| Apple ProRes 4444 |Sim |
| Apple ProRes 4444 XQ |Sim |
| HEVC/H.265| Perfil principal|

## <a name="input-audio-codecs"></a>Codificadores de áudio de entrada
| Codificadores de áudio de entrada | Suportado |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |
| Camada MPEG 2 |Sim |
| MP3 (Camada áudio MPEG-1 3) |Sim |
| Windows Media Audio |Sim |
| WAV/PCM |Sim |
| </a> [flac](https://en.wikipedia.org/wiki/FLAC) |Sim |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Sim |
| [</a> Vorbis](https://en.wikipedia.org/wiki/Vorbis) |Sim |
| Rama (adaptativo multi-taxa) |Sim |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |
| Dolby® E |Não |
| Dolby® Digital (AC3) |Não |
| Dolby® Digital Plus (E-AC3) |Não |

## <a name="output-formats-and-codecs"></a>Formatos de saída e codecs
A tabela que se segue lista os códigos e os formatos de ficheiros que são suportados para exportação.

| Formato do Ficheiro | Código de Vídeo | Código de Áudio |
| --- | --- | --- |
| MP4 <br/><br/>(incluindo recipientes MP4 multibitados) |H.264 (Perfis altos, principais e de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (Perfis altos, principais e de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Passos seguintes

[Criar uma transformação com um preset personalizado](customize-encoder-presets-how-to.md)

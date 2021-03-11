---
title: Formatos e códigos Standard Encoder - Azure
description: Este artigo contém uma lista dos formatos de ficheiros de importação e exportação mais comuns que pode utilizar com o StandardEncoderPreset.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: f0cf89a755090010bb80507c52f90baa2a4e804a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617629"
---
# <a name="standard-encoder-formats-and-codecs"></a>Formatos e códigos Standard Encoder

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo contém uma lista dos formatos de ficheiros de importação e exportação mais comuns que pode utilizar com [o StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Para obter informações sobre como criar predefinições personalizadas utilizando **o StandardEncoderPreset,** consulte [Criar uma transformação com uma predefinição personalizada](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Formatos de recipiente/ficheiro de entrada

| Formatos de ficheiro (extensões de ficheiro) | Suportado |
| --- | --- |
| FLV (com codecs H.264 e AAC) (.flv) |Yes |
| MXF    (.mxf) |Yes |
| GXF    (.gxf) |Yes |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Yes |
| Vídeo de Suporte de Dados do Windows (WMV)/ASF (.wmv, .asf) |Yes |
| AVI (Descomprimido 8 bits/10 bits) (.avi) |Yes |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Yes |
| [Gravação de Vídeo Digital da Microsoft(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Yes |
| Matroska/WebM (.mkv) |Yes |
| WAVE/WAV (.wav) |Yes |
| QuickTime (.mov) |Yes |

### <a name="audio-formats-in-input-containers"></a>Formatos áudio em recipientes de entrada

A Standard Encoder suporta o transporte dos seguintes formatos áudio em recipientes de entrada:

* Ficheiros MXF, GXF e QuickTime, que têm faixas áudio com amostras de som intercalada ou 5,1

ou

* ficheiros do MXF, do GXF e do QuickTime onde o áudio é executado como faixas PCM em separado, mas o mapeamento do canal (para estéreo ou 5.1) pode ser reduzido a partir dos metadados de ficheiro

## <a name="input-video-codecs"></a>Inserir códigos de vídeo
| Inserir códigos de vídeo | Suportado |
| --- | --- |
| AVC 8-bits/10-bits, até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |
| Avid DNxHD (no MXF) |Yes |
| DVCPro/DVCProHD (no MXF) |Yes |
| Vídeo digital (DV) (em ficheiros AVI) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (até Perfil 422 e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até Perfil 422 |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |No |
| MPEG-4 Parte 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420 descomprimido ou mezanino |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H.265| Perfil Principal|

## <a name="input-audio-codecs"></a>Inserir códigos de áudio
| Codecs de Entrada de Áudio | Suportado |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Yes |
| MPEG Camada 2 |Yes |
| MP3 (MPEG-1 Camada de Áudio 3) |Yes |
| Áudio de Suporte de Dados do Windows |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (taxa multi adaptável) |Yes |
| AES (SMPTE 331M e 302M, AES3-2003) |No |
| Dolby® E |No |
| Dolby® Digital (AC3) |No |
| Dolby® Digital Plus (E-AC3) |No |

## <a name="output-formats-and-codecs"></a>Formatos de saída e codecs
A tabela que se segue lista os formatos de códigos e ficheiros suportados para exportação.

| Formato do Ficheiro | Código de vídeo | Codec áudio |
| --- | --- | --- |
| MP4 <br/><br/>(incluindo recipientes MP4 multi-bitrate) |H.264 (Perfis altos, principais e de base), HEVC (H.265) 8-bit |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (Perfis altos, principais e de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Passos seguintes

[Criar uma transformação com uma predefinição personalizada](customize-encoder-presets-how-to.md)

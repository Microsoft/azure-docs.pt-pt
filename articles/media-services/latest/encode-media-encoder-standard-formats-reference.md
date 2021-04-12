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
ms.openlocfilehash: 2fe8f5d0acb2ab7e2f1c64dde1bedb19b96111e9
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492295"
---
# <a name="standard-encoder-formats-and-codecs"></a>Formatos e códigos Standard Encoder

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo contém uma lista dos formatos de ficheiros de importação e exportação mais comuns que pode utilizar com [o StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Para obter informações sobre como criar predefinições personalizadas utilizando **o StandardEncoderPreset,** consulte [Criar uma transformação com uma predefinição personalizada](transform-custom-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Formatos de recipiente/ficheiro de entrada

| Formatos de ficheiro (extensões de ficheiro) | Suportado |
| --- | --- |
| FLV (com codecs H.264 e AAC) (.flv) |Sim |
| MXF    (.mxf) |Sim |
| GXF    (.gxf) |Sim |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Sim |
| Vídeo de Suporte de Dados do Windows (WMV)/ASF (.wmv, .asf) |Sim |
| AVI (Descomprimido 8 bits/10 bits) (.avi) |Sim |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Sim |
| [Gravação de Vídeo Digital da Microsoft(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Sim |
| Matroska/WebM (.mkv) |Sim |
| WAVE/WAV (.wav) |Sim |
| QuickTime (.mov) |Sim |

### <a name="audio-formats-in-input-containers"></a>Formatos áudio em recipientes de entrada

A Standard Encoder suporta o transporte dos seguintes formatos áudio em recipientes de entrada:

* Ficheiros MXF, GXF e QuickTime, que têm faixas áudio com amostras de som intercalada ou 5,1

ou

* ficheiros do MXF, do GXF e do QuickTime onde o áudio é executado como faixas PCM em separado, mas o mapeamento do canal (para estéreo ou 5.1) pode ser reduzido a partir dos metadados de ficheiro

## <a name="input-video-codecs"></a>Inserir códigos de vídeo
| Inserir códigos de vídeo | Suportado |
| --- | --- |
| AVC 8-bits/10-bits, até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |
| Avid DNxHD (no MXF) |Sim |
| DVCPro/DVCProHD (no MXF) |Sim |
| Vídeo digital (DV) (em ficheiros AVI) |Sim |
| JPEG 2000 |Sim |
| MPEG-2 (até Perfil 422 e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até Perfil 422 |
| MPEG-1 |Sim |
| VC-1/WMV9 |Sim |
| Canopus HQ/HQX |No |
| MPEG-4 Parte 2 |Sim |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |
| YUV420 descomprimido ou mezanino |Sim |
| Apple ProRes 422 |Sim |
| Apple ProRes 422 LT |Sim |
| Apple ProRes 422 HQ |Sim |
| Apple ProRes Proxy |Sim |
| Apple ProRes 4444 |Sim |
| Apple ProRes 4444 XQ |Sim |
| HEVC/H.265| Perfil Principal|

## <a name="input-audio-codecs"></a>Inserir códigos de áudio
| Codecs de Entrada de Áudio | Suportado |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |
| MPEG Camada 2 |Sim |
| MP3 (MPEG-1 Camada de Áudio 3) |Sim |
| Áudio de Suporte de Dados do Windows |Sim |
| WAV/PCM |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Sim |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |
| AMR (taxa multi adaptável) |Sim |
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

[Criar uma transformação com uma predefinição personalizada](transform-custom-presets-how-to.md)

---
title: Media Encoder Formatos e códigos standard - Azure
description: Este artigo fornece uma visão geral dos formatos e códigos Standard da Media Encoder.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: 7aada042254a2794602002614df51646be90c7cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013147"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Formatos e Codecs do Codificador de Multimédia Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 2](media-services-media-encoder-standard-formats.md)
> * [Versão 3](../latest/media-encoder-standard-formats.md)

Este documento contém uma lista dos formatos de ficheiros de importação e exportação mais comuns que pode utilizar com a Media Encoder Standard.

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

> [!NOTE]
> Acima encontra-se uma lista das extensões de ficheiro encontradas mais frequentemente. Media Encoder Standard suporta muitos outros (por exemplo: .m2ts, .mpeg2video, .qt). Se tentar codificar um ficheiro e receber uma mensagem de erro sobre o formato não estar a ser suportado, forneça [aqui](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)o seu feedback .
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formatos áudio em recipientes de entrada
A Media Encoder Standard suporta o transporte dos seguintes formatos áudio em recipientes de entrada:

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
| HEVC/H.265| Perfis Principais e Principais 10 (&#42;)<br/>Suporte principal de perfil 10 destina-se ao conteúdo de 8bit 4:2:0. |

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
| MP4 <br/><br/>(incluindo recipientes MP4 multi-bitrate) |H.264 (Perfis altos, principais e de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (Perfis altos, principais e de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ver também
[Codificação de conteúdos a pedido com serviços de mídia Azure](media-services-encode-asset.md)

[Como codificar com o Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

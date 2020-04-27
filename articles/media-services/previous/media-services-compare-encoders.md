---
title: Comparação de Azure sobre os codificadores de meios de comunicação de procura [ Microsoft Docs
description: Este tópico compara as capacidades de codificação da **Media Encoder Standard** e **Media Encoder Premium Workflow**.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "69016660"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparação de Azure sobre codificadores de meios de comunicação de procura  

Este tópico compara as capacidades de codificação da **Media Encoder Standard** e **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Capacidades de processamento de vídeo e áudio

O quadro seguinte compara a funcionalidade entre media Encoder Standard (MES) e Media Encoder Premium Workflow (MEPW). 

|Capacidade|Media Encoder Standard|Fluxo de Trabalho Premium de Codificador de Multimédia|
|---|---|---|
|Aplicar lógica condicional durante a codificação<br/>(por exemplo, se a entrada for HD, em seguida, incidar áudio 5.1)|Não|Sim|
|Legendagem de áudio|Não|[Sim](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Correção de Alto Nível Profissional do Dolby®](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> com a Inteligência do Diálogo™|Não|Sim|
|Telecine des-interalocada e inversa|Básico|Qualidade de transmissão|
|Detetar e remover fronteiras negras <br/>(caixas de pilares, caixas de correio)|Não|Sim|
|Geração de miniaturas|[Sim](media-services-dotnet-generate-thumbnail-with-mes.md)|[Sim](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Clipping/aparar e costurar de vídeos|[Sim](media-services-advanced-encoding-with-mes.md#trim_video)|Sim|
|Sobreposições de áudio ou vídeo|[Sim](media-services-advanced-encoding-with-mes.md#overlay)|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Sobreposições de gráficos|A partir de fontes de imagem|A partir de fontes de imagem e texto|
|Múltiplas faixas de linguagem áudio|Limitado|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Contador de faturação usado por cada codificador
| Nome do processador de mídia | Preços aplicáveis | Notas |
| --- | --- | --- |
| **Media Encoder Standard** |CODIFICAdor |As tarefas de codificação serão cobradas com base na duração total, em minutos, de todos os ficheiros de mídia produzidos como saída, à taxa [aqui][1]especificada, sob a coluna ENCODER. |
| **Fluxo de Trabalho Premium de Codificador de Multimédia** |PREMIUM ENCODER |As tarefas de codificação serão cobradas com base na duração total, em minutos, de todos os ficheiros de mídia produzidos como saída, à taxa [aqui][1]especificada, sob a coluna PREMIUM ENCODER. |

## <a name="input-containerfile-formats"></a>Formatos de recipiente de entrada/ficheiro
| Contentor de Entrada/Formatos de Ficheiro | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Sim |Sim |
| MXF/SMPTE 377m |Sim |Sim |
| GXF |Sim |Sim |
| Fluxos de transporte MPEG-2 |Sim |Sim |
| Fluxos de programas MPEG-2 |Sim |Sim |
| MPEG-4/MP4 |Sim |Sim |
| Windows Media/ASF |Sim |Sim |
| AVI (Não comprimido 8bit/10bit) |Sim |Sim |
| 3GPP/3GPP2 |Sim |Não |
| Formato de ficheiro de streaming suave (PIFF 1.3) |Sim |Não |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Sim |Não |
| Matroska/WebM |Sim |Não |
| QuickTime (.mov) |Sim |Não |

## <a name="input-video-codecs"></a>Input video codecs
| Codecs de Vídeo de Entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC 8-bits/10-bits, até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |Sim |
| Avid DNxHD (no MXF) |Sim |Sim |
| DVCPro/DVCProHD (no MXF) |Sim |Sim |
| JPEG2000 |Sim |Sim |
| MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até Perfil 422 |Sim |
| MPEG-1 |Sim |Sim |
| Windows Media Video/VC-1 |Sim |Sim |
| Canopus HQ/HQX |Não |Não |
| MPEG-4 Parte 2 |Sim |Não |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |Não |
| Apple ProRes 422 |Sim |Não |
| Apple ProRes 422 LT |Sim |Não |
| Apple ProRes 422 HQ |Sim |Não |
| Apple ProRes Proxy |Sim |Não |
| Apple ProRes 4444 |Sim |Não |
| Apple ProRes 4444 XQ |Sim |Não |
| HEVC/H.265|Perfil principal|Perfil principal e principal 10|

## <a name="input-audio-codecs"></a>Codificadores de áudio de entrada
| Codecs de Entrada de Áudio | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |Sim |
| Dolby® E |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (E-AC3) |Não |Sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG Camada 2 |Sim |Sim |
| MP3 (MPEG-1 Camada de Áudio 3) |Sim |Sim |
| Áudio de Suporte de Dados do Windows |Sim |Sim |
| WAV/PCM |Sim |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |Não |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Sim |Não |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |Não |

## <a name="output-containerfile-formats"></a>Formatos de recipiente de saída/ficheiro
| Recipiente de saída/formatos de ficheiro | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Não |Sim |
| MXF (OP1a, XDCAM e AS02) |Não |Sim |
| DPP (incluindo AS11) |Não |Sim |
| GXF |Não |Sim |
| MPEG-4/MP4 |Sim |Sim |
| MPEG-TS |Sim |Sim |
| Windows Media/ASF |Não |Sim |
| AVI (Não comprimido 8bit/10bit) |Não |Sim |
| Formato de ficheiro de streaming suave (PIFF 1.3) |Não |Sim |

## <a name="output-video-codecs"></a>Códigos de vídeo de saída
| Códigos de vídeo de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC (H.264; 8 bits; até High Profile, Nível 5.2; 4K Ultra HD; AVC Intra) |Apenas 8 bit 4:2:0 |Sim |
| HEVC (H.265; 8 bits e 10-bit;)  |Não |Sim |
| Avid DNxHD (no MXF) |Não |Sim |
| MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Não |Sim |
| MPEG-1 |Não |Sim |
| Windows Media Video/VC-1 |Não |Sim |
| Criação de miniaturas JPEG |Sim |Sim |
| Criação de miniaturas png |Sim |Sim |
| Criação de miniaturas BMP |Sim |Não |

## <a name="output-audio-codecs"></a>Codificadores de áudio de saída
| Códigos áudio de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (E-AC3) até 7.1 |Não |Sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG Camada 2 |Não |Sim |
| MP3 (MPEG-1 Camada de Áudio 3) |Não |Sim |
| Áudio de Suporte de Dados do Windows |Não |Sim |

>[!NOTE]
>Se codificar o Dolby® Digital (AC3), a saída só pode ser escrita num ficheiro ISO MP4.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Execute tarefas avançadas de codificação personalizando predefinições padrão do Media Encoder](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e Limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/

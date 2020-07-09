---
title: Comparação do Azure sobre os codificadores de mídia à procura Microsoft Docs
description: Este tópico compara as capacidades de codificação da **Media Encoder Standard** e **media Encoder Premium Workflow**.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "69016660"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparação de Azure sobre codificadores de meios de comunicação a pedido  

Este tópico compara as capacidades de codificação da **Media Encoder Standard** e **media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Capacidades de processamento de vídeo e áudio

O quadro seguinte compara a funcionalidade entre a Media Encoder Standard (MES) e o Media Encoder Premium Workflow (MEPW). 

|Funcionalidade|Media Encoder Standard|Fluxo de Trabalho Premium de Codificador de Multimédia|
|---|---|---|
|Aplicar lógica condicional ao codificar<br/>(por exemplo, se a entrada for HD, então codificar 5.1 áudio)|Não|Yes|
|Legendagem de áudio|No|[Sim](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Correção de ruído profissional ® Dolby](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> com a Inteligência do Diálogo™|Não|Yes|
|Desativação, telecina inversa|Básica|Qualidade de transmissão|
|Detetar e remover fronteiras negras <br/>(caixas de pilares, caixas de correio)|Não|Yes|
|Geração de miniaturas|[Sim](media-services-dotnet-generate-thumbnail-with-mes.md)|[Sim](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Recorte/aparar e costurar de vídeos|[Sim](media-services-advanced-encoding-with-mes.md#trim_video)|Yes|
|Sobreposições de áudio ou vídeo|[Sim](media-services-advanced-encoding-with-mes.md#overlay)|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Sobreposição de gráficos|A partir de fontes de imagem|A partir de fontes de imagem e texto|
|Múltiplas faixas de linguagem áudio|Limitado|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Contador de faturação usado por cada codificadora
| Nome do processador de mídia | Preços aplicáveis | Notas |
| --- | --- | --- |
| **Media Encoder Standard** |CODIFICAÇÃO |As Tarefas de Codificação serão carregadas com base na duração total, em minutos, de todos os ficheiros de mídia produzidos como saída, à taxa aqui [especificada][1], sob a coluna ENCODER. |
| **Fluxo de Trabalho Premium de Codificador de Multimédia** |CODIFICAÇÃO PREMIUM |As Tarefas de Codificação serão carregadas com base na duração total, em minutos, de todos os ficheiros de mídia produzidos como saída, à taxa aqui [especificada][1], sob a coluna PREMIUM ENCODER. |

## <a name="input-containerfile-formats"></a>Formatos de recipiente/ficheiro de entrada
| Contentor de Entrada/Formatos de Ficheiro | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Sim |Sim |
| MXF/SMPTE 377M |Sim |Sim |
| GXF |Sim |Sim |
| Fluxos de transporte MPEG-2 |Sim |Sim |
| Streams de Programas MPEG-2 |Sim |Sim |
| MPEG-4/MP4 |Sim |Sim |
| Windows Media/ASF |Sim |Sim |
| AVI (8bit/10bit descomprimido) |Sim |Sim |
| 3GPP/3GPP2 |Yes |Não |
| Formato de ficheiro de streaming suave (PIFF 1.3) |Yes |Não |
| [Gravação de vídeo digital da Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Yes |Não |
| Matroska/WebM |Yes |Não |
| QuickTime (.mov) |Yes |Não |

## <a name="input-video-codecs"></a>Inserir códigos de vídeo
| Codecs de Vídeo de Entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC 8-bits/10-bits, até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |Yes |
| Avid DNxHD (no MXF) |Sim |Sim |
| DVCPro/DVCProHD (no MXF) |Sim |Sim |
| JPEG2000 |Sim |Sim |
| MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até Perfil 422 |Yes |
| MPEG-1 |Sim |Sim |
| Windows Media Video/VC-1 |Sim |Sim |
| Canopus HQ/HQX |Não |Não |
| MPEG-4 Parte 2 |Yes |No |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |No |
| Apple ProRes 422 |Yes |No |
| Apple ProRes 422 LT |Yes |No |
| Apple ProRes 422 HQ |Yes |No |
| Apple ProRes Proxy |Yes |No |
| Apple ProRes 4444 |Yes |No |
| Apple ProRes 4444 XQ |Yes |No |
| HEVC/H.265|Perfil Principal|Perfil principal e principal 10|

## <a name="input-audio-codecs"></a>Inserir códigos de áudio
| Codecs de Entrada de Áudio | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |No |Yes |
| Dolby® E |No |Yes |
| Dolby® Digital (AC3) |No |Yes |
| Dolby® Digital Plus (E-AC3) |No |Yes |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG Camada 2 |Sim |Sim |
| MP3 (MPEG-1 Camada de Áudio 3) |Sim |Sim |
| Áudio de Suporte de Dados do Windows |Sim |Sim |
| WAV/PCM |Sim |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |No |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |No |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |No |

## <a name="output-containerfile-formats"></a>Formatos de contentor/ficheiro de saída
| Formatos de contentores/ficheiros de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |No |Yes |
| MXF (OP1a, XDCAM e AS02) |No |Yes |
| DPP (incluindo AS11) |Não |Yes |
| GXF |Não |Yes |
| MPEG-4/MP4 |Sim |Sim |
| MPEG-TS |Sim |Sim |
| Windows Media/ASF |Não |Yes |
| AVI (8bit/10bit descomprimido) |Não |Yes |
| Formato de ficheiro de streaming suave (PIFF 1.3) |Não |Yes |

## <a name="output-video-codecs"></a>Codecs de vídeo de saída
| Códigos de vídeo de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC (H.264; 8-bit; até High Profile, Nível 5.2; 4K Ultra HD; AVC Intra) |Apenas 8 bit 4:2:0 |Yes |
| HEVC (H.265; 8-bit e 10-bit;)  |Não |Yes |
| Avid DNxHD (no MXF) |Não |Yes |
| MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Não |Yes |
| MPEG-1 |Não |Yes |
| Windows Media Video/VC-1 |Não |Yes |
| Criação de miniatura JPEG |Sim |Sim |
| Criação de miniaturas de PNG |Sim |Sim |
| Criação de miniaturas BMP |Yes |Não |

## <a name="output-audio-codecs"></a>Codecs de áudio de saída
| Codecs de áudio de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |Yes |
| Dolby® Digital (AC3) |Não |Yes |
| Dolby® Digital Plus (E-AC3) até 7.1 |Não |Yes |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG Camada 2 |Não |Yes |
| MP3 (MPEG-1 Camada de Áudio 3) |Não |Yes |
| Áudio de Suporte de Dados do Windows |Não |Yes |

>[!NOTE]
>Se codificar para Dolby® Digital (AC3), a saída só pode ser escrita num ficheiro MP4 ISO.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Execute tarefas avançadas de codificação personalizando predefinições padrão media Encoder](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e Limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/

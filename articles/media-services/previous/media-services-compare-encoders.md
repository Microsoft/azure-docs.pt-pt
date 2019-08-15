---
title: Comparação de codificadores de mídia sob demanda do Azure | Microsoft Docs
description: Este tópico compara os recursos de codificação de **Media Encoder Standard** e **Media Encoder Premium Workflow**.
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
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016660"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparação de codificadores de mídia sob demanda do Azure  

Este tópico compara os recursos de codificação de **Media Encoder Standard** e **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Recursos de processamento de vídeo e áudio

A tabela a seguir compara a funcionalidade entre Media Encoder Standard (MES) e Media Encoder Premium Workflow (MEPW). 

|Funcionalidade|Media Encoder Standard|Fluxo de Trabalho Premium de Codificador de Multimédia|
|---|---|---|
|Aplicar a lógica condicional durante a codificação<br/>(por exemplo, se a entrada for HD, codifique 5,1 áudio)|Não|Sim|
|Legendagem oculta|Não|[Sim](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Correção de intensidade do Dolby® Professional](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> com inteligência de diálogo™|Não|Sim|
|Desentrelaçamento, telecineon inverso|Básica|Qualidade de difusão|
|Detectar e remover bordas pretas <br/>(pillarboxes, letterboxes)|Não|Sim|
|Geração de miniatura|[Sim](media-services-dotnet-generate-thumbnail-with-mes.md)|[Sim](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Recorte/corte e costura de vídeos|[Sim](media-services-advanced-encoding-with-mes.md#trim_video)|Sim|
|Sobreposições de áudio ou vídeo|[Sim](media-services-advanced-encoding-with-mes.md#overlay)|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Sobreposições de gráficos|De fontes de imagem|De fontes de imagem e texto|
|Várias faixas de idioma de áudio|Limitado|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Medidor de cobrança usado por cada codificador
| Nome do processador de mídia | Preços aplicáveis | Notas |
| --- | --- | --- |
| **Media Encoder Standard** |FITA |As tarefas de codificação serão cobradas com base na duração total, em minutos, de todos os arquivos de mídia produzidos como saída, na taxa especificada [aqui][1], na coluna do codificador. |
| **Fluxo de Trabalho Premium do Codificador de Multimédia** |CODIFICADOR PREMIUM |As tarefas de codificação serão cobradas com base na duração total, em minutos, de todos os arquivos de mídia produzidos como saída, na taxa especificada [aqui][1], na coluna codificador Premium. |

## <a name="input-containerfile-formats"></a>Formatos de contêiner/arquivo de entrada
| Formatos de contêiner/arquivo de entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Sim |Sim |
| 377M MXF/SMPTE |Sim |Sim |
| GXF |Sim |Sim |
| Fluxos de transporte MPEG-2 |Sim |Sim |
| Fluxos de programa MPEG-2 |Sim |Sim |
| MPEG-4/MP4 |Sim |Sim |
| Windows Media/ASF |Sim |Sim |
| AVI (8 bits/descompactado não compactados) |Sim |Sim |
| 3GPP/3GPP2 |Sim |Não |
| Formato de arquivo de Smooth Streaming (PIFF 1,3) |Sim |Não |
| [Gravação de vídeo digital da Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Sim |Não |
| Matroska/WebM |Sim |Não |
| QuickTime (. mov) |Sim |Não |

## <a name="input-video-codecs"></a>Codecs de vídeo de entrada
| Codecs de vídeo de entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC de 8 bits/10 bits, até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |Sim |
| Ávido DNxHD (em MXF) |Sim |Sim |
| DVCPro/DVCProHD (em MXF) |Sim |Sim |
| JPEG2000 |Sim |Sim |
| MPEG-2 (até 422 de perfil e alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até 422 perfil |Sim |
| MPEG-1 |Sim |Sim |
| Windows Media Video/VC-1 |Sim |Sim |
| Canopus HQ/HQX |Não |Não |
| MPEG-4 parte 2 |Sim |Não |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |Não |
| Apple ProRes 422 |Sim |Não |
| Apple ProRes 422 LT |Sim |Não |
| Apple ProRes 422 HQ |Sim |Não |
| Proxy Apple ProRes |Sim |Não |
| Apple ProRes 4444 |Sim |Não |
| Apple ProRes 4444 XQ |Sim |Não |
| HEVC/H. 265|Perfil principal|Perfil 10 principal e principal|

## <a name="input-audio-codecs"></a>Codecs de áudio de entrada
| Codecs de áudio de entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |Sim |
| Dolby® E |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (E-AC3) |Não |Sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5,1) |Sim |Sim |
| Camada 2 MPEG |Sim |Sim |
| MP3 (MPEG-1 áudio camada 3) |Sim |Sim |
| Áudio do Windows Media |Sim |Sim |
| WAV/PCM |Sim |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |Não |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Sim |Não |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |Não |

## <a name="output-containerfile-formats"></a>Formatos de contêiner/arquivo de saída
| Formatos de contêiner/arquivo de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Não |Sim |
| MXF (OP1a, XDCAM e AS02) |Não |Sim |
| DPP (incluindo AS11) |Não |Sim |
| GXF |Não |Sim |
| MPEG-4/MP4 |Sim |Sim |
| MPEG-TS |Sim |Sim |
| Windows Media/ASF |Não |Sim |
| AVI (8 bits/descompactado não compactados) |Não |Sim |
| Formato de arquivo de Smooth Streaming (PIFF 1,3) |Não |Sim |

## <a name="output-video-codecs"></a>Codecs de vídeo de saída
| Codecs de vídeo de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC (H. 264; 8 bits; até o alto perfil, nível 5,2; 4K ultra HD; AVC intra) |Somente 8 bits 4:2:0 |Sim |
| HEVC (H. 265; 8 bits e 10 bits;)  |Não |Sim |
| Ávido DNxHD (em MXF) |Não |Sim |
| MPEG-2 (até 422 de perfil e alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Não |Sim |
| MPEG-1 |Não |Sim |
| Windows Media Video/VC-1 |Não |Sim |
| Criação de miniatura JPEG |Sim |Sim |
| Criação de miniaturas PNG |Sim |Sim |
| Criação de miniaturas BMP |Sim |Não |

## <a name="output-audio-codecs"></a>Codecs de áudio de saída
| Codecs de áudio de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (E-AC3) até 7,1 |Não |Sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5,1) |Sim |Sim |
| Camada 2 MPEG |Não |Sim |
| MP3 (MPEG-1 áudio camada 3) |Não |Sim |
| Áudio do Windows Media |Não |Sim |

>[!NOTE]
>Se você codificar para Dolby® Digital (AC3), a saída só poderá ser gravada em um arquivo. MP4 ISO.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas de codificação avançadas Personalizando predefinições de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Cotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/

---
title: Comparação do Azure no codificadores de multimédia a pedido | Documentos da Microsoft
description: Este tópico compara as capacidades de codificação de **Media Encoder Standard** e **Media Encoder Premium Workflow**.
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
ms.author: juliako;anilmur
ms.openlocfilehash: bb827b80f79a53f30074b9230efe3e2049471051
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880777"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparação do Azure no codificadores de multimédia a pedido  

Este tópico compara as capacidades de codificação de **Media Encoder Standard** e **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Recursos de processamento de áudio e vídeo

A tabela seguinte compara as funcionalidades entre o Media Encoder Standard (MES) e o Media Encoder Premium Workflow (MEPW). 

|Capacidade|Media Encoder Standard|Fluxo de Trabalho Premium de Codificador de Multimédia|
|---|---|---|
|Aplicar lógica condicional durante a codificação<br/>(por exemplo, se a entrada for HD, em seguida, codificar áudio 5.1)|Não|Sim|
|Legendagem de áudio|Não|[Sim](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Correção de intensidade profissional Dolby®](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> com a caixa de diálogo Intelligence™|Não|Sim|
|Telecine inversa, remover a atribuição do entrelaçamento|Básica|Qualidade de difusão|
|Detecção e remoção de bordas pretas <br/>(pillarboxes, letterboxes)|Não|Sim|
|Geração de miniaturas|[Sim](media-services-dotnet-generate-thumbnail-with-mes.md)|[Sim](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|/ Remoção de recorte e clips de vídeos|[Sim](media-services-advanced-encoding-with-mes.md#trim_video)|Sim|
|Sobreposições de áudio ou vídeo|[Sim](media-services-advanced-encoding-with-mes.md#overlay)|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Sobreposições de gráficos|De origens de imagem|A partir de origens de imagem e texto|
|Vários roteiros de idiomas de áudio|Limitado|[Sim](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Medidores de faturação utilizado por cada codificador
| Nome do processador de multimédia | Os preços aplicáveis | Notas |
| --- | --- | --- |
| **Media Encoder Standard** |CODIFICADOR |Codificação tarefas são cobradas com base na duração total, em minutos, todos os ficheiros de suporte de dados produzidos como saída, a taxa especificada [aqui][1], sob a coluna de CODIFICADOR. |
| **Fluxo de Trabalho Premium do Codificador de Multimédia** |CODIFICADOR PREMIUM |Codificação tarefas são cobradas com base na duração total, em minutos, todos os ficheiros de suporte de dados produzidos como saída, a taxa especificada [aqui][1], sob a coluna de CODIFICADOR PREMIUM. |

## <a name="input-containerfile-formats"></a>Formatos de arquivo/contentor de entrada
| Formatos de arquivo/contentor de entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Sim |Sim |
| MXF/SMPTE 377M |Sim |Sim |
| GXF |Sim |Sim |
| Fluxos de transporte de MPEG-2 |Sim |Sim |
| Fluxos do programa de MPEG-2 |Sim |Sim |
| MPEG-4/MP4 |Sim |Sim |
| Windows Media/ASF |Sim |Sim |
| AVI (descomprimido 8 bits/10 bits) |Sim |Sim |
| 3GPP/3GPP2 |Sim |Não |
| Formato de ficheiro transmissão em fluxo uniforme (PIFF 1.3) |Sim |Não |
| [Recording(DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Sim |Não |
| Matroska/WebM |Sim |Não |
| QuickTime (.mov) |Sim |Não |

## <a name="input-video-codecs"></a>Codecs de vídeo de entrada
| Codecs de vídeo de entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC 8 bits/10-bits, até 4:2:2, incluindo AVCIntra |4 de 8 bits: 2:0 e 4:2:2 |Sim |
| Avid DNxHD (no MXF) |Sim |Sim |
| DVCPro/DVCProHD (no MXF) |Sim |Sim |
| JPEG2000 |Sim |Sim |
| MPEG-2 (até perfil 422 e alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Até perfil 422 |Sim |
| MPEG-1 |Sim |Sim |
| Windows Media vídeo/VC-1 |Sim |Sim |
| Canopus HQ/HQX |Não |Não |
| MPEG-4 parte 2 |Sim |Não |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sim |Não |
| Apple ProRes 422 |Sim |Não |
| Apple ProRes 422 LT |Sim |Não |
| Apple ProRes 422 HQ |Sim |Não |
| Apple ProRes Proxy |Sim |Não |
| Apple ProRes 4444 |Sim |Não |
| Apple ProRes 4444 XQ |Sim |Não |
| HEVC/H.265|Perfil de principal|Principal e o perfil de Main 10|

## <a name="input-audio-codecs"></a>Codecs de áudio de entrada
| Codecs de áudio de entrada | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331m e 302 M, AES3 2003) |Não |Sim |
| Dolby® E |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (E-AC3) |Não |Sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG camada 2 |Sim |Sim |
| MP3 (MPEG-1 camada de áudio 3) |Sim |Sim |
| Windows Media Audio |Sim |Sim |
| WAV/PCM |Sim |Sim |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sim |Não |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Sim |Não |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sim |Não |

## <a name="output-containerfile-formats"></a>Formatos de arquivo/contêiner de saída
| Formatos de arquivo/contêiner de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| Adobe® Flash® F4V |Não |Sim |
| Ficheiros do MXF (OP1a, XDCAM e AS02) |Não |Sim |
| O protocolo DPP (incluindo AS11) |Não |Sim |
| GXF |Não |Sim |
| MPEG-4/MP4 |Sim |Sim |
| MPEG-TS |Sim |Sim |
| Windows Media/ASF |Não |Sim |
| AVI (descomprimido 8 bits/10 bits) |Não |Sim |
| Formato de ficheiro transmissão em fluxo uniforme (PIFF 1.3) |Não |Sim |

## <a name="output-video-codecs"></a>Codecs de vídeo de saída
| Codecs de vídeo de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AVC (H.264; de 8 bits; até o perfil de alto nível 5.2; 4 K Ultra em alta definição; Dentro de AVC) |4 de bit de 8 apenas: 2:0 |Sim |
| HEVC (H.265; 8 bits e 10 bits;)  |Não |Sim |
| Avid DNxHD (no MXF) |Não |Sim |
| MPEG-2 (até perfil 422 e alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10) |Não |Sim |
| MPEG-1 |Não |Sim |
| Windows Media vídeo/VC-1 |Não |Sim |
| Criação de miniaturas de JPEG |Sim |Sim |
| Criação de miniaturas de PNG |Sim |Sim |
| Criação de miniaturas de BMP |Sim |Não |

## <a name="output-audio-codecs"></a>Codecs de áudio de saída
| Codecs de áudio de saída | Media Encoder Standard | Fluxo de Trabalho Premium de Codificador de Multimédia |
| --- | --- | --- |
| AES (SMPTE 331m e 302 M, AES3 2003) |Não |Sim |
| Dolby® Digital (AC3) |Não |Sim |
| Dolby® Digital Plus (E-AC3) até 7.1 |Não |Sim |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |Sim |Sim |
| MPEG camada 2 |Não |Sim |
| MP3 (MPEG-1 camada de áudio 3) |Não |Sim |
| Windows Media Audio |Não |Sim |

>[!NOTE]
>Se a codificar em Dolby® Digital (AC3), a saída só pode ser escrita em ficheiros MP4 de ISO.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas de codificação avançadas ao personalizar as predefinições do Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/

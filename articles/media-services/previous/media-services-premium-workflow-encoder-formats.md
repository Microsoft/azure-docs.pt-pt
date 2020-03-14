---
title: Media Encoder Premium Workflow formatos e codecs / Microsoft Docs
description: Este tópico dá uma visão geral dos formatos e códigos de fluxo de trabalho do Media Encoder Premium
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269760"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium Workflow formatos e codecs

> [!NOTE]
> O processador de mídia Media Encoder Premium Workflow, discutido neste tópico, não está disponível na China. 

Este documento contém uma lista de formatos e códigos de ficheirode entrada e saída que são suportados pela versão de pré-visualização pública do **codificador de trabalho Media Encoder Premium Workflow.**

[Media Encoder Premium Workflow Input Formatos e Codecs](#input_formats)

Media Encoder Premium Workflow Output Formatos e Codecs

**Media Encoder Premium Workflow** suporta legendagem fechada descrita [nesta](#closed_captioning) secção. 

## <a id="input_formats"></a>Media Encoder Premium Workflow Input Formatos e Codecs

A secção seguinte lista os códigos e formatos de ficheiros que este processador de mídia suporta como entrada.

### <a name="input-containerfile-formats"></a>Formatos de recipiente de entrada/ficheiro

* Adobe® Flash® F4V
* MXF/SMPTE 377m
* GXF
* Fluxos de transporte MPEG-2
* Fluxos de programas MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (Não comprimido 8bit/10bit)

### <a name="input-video-codecs"></a>Input Video Codecs

* AVC 8-bit/10-bit, até 4:2:2, incluindo AVCIntra
* DNxHD ávido (em MXF)
* DVCPro/DVCProHD (em MXF)
* Hevc/H.265, Perfil Principal e Principal 10
* JPEG2000
* MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Codificadores de áudio de entrada

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
* Camada MPEG 2
* MP3 (Camada áudio MPEG-1 3)
* Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>Media Encoder Premium Workflow Output Formatos e Codecs

A secção seguinte lista os códigos e formatos de ficheiros que são suportados como saída deste processador de mídia.

### <a name="output-containerfile-formats"></a>Recipiente de saída/formatos de ficheiro

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM e AS02)
* DPP (incluindo AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (Não comprimido 8bit/10bit)
* Formato de ficheiro de streaming suave (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Códigos de vídeo de saída

* AVC (H.264; 8 bits; até High Profile, Nível 5.2; 4K Ultra HD; AVC Intra)
* DNxHD ávido (em MXF)
* DVCPro/DVCProHD (em MXF)
* MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Windows Media Video/VC-1
* Criação de miniaturas JPEG
* HEVC (H.265; 8 bits e 10 bits, Main and Main 10 Profile)


### <a name="output-audio-codecs"></a>Códigos áudio de saída

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) até 7.1
* AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
* Camada MPEG 2
* MP3 (Camada áudio MPEG-1 3)
* Windows Media Audio

>[!NOTE]
>Se codificar o Dolby® Digital (AC3), a saída só pode ser escrita num ficheiro ISO MP4.

## <a id="closed_captioning"></a>Suporte para Legendagem Fechada

Na ingestão, suportes de trabalho premium media **encoder:**

1. Ficheiros SCC
2. Ficheiros SMPTE-TT
3. CEA-608/CEA-708 – transportados como dados de utilizador (mensagens SEI de streams elementares H.264, ATSC/53, SCTE20) ou transportados como dados auxiliares nos ficheiros MXF/GXF
4. Ficheiros de legendas STL

Na saída, estão disponíveis as seguintes opções:

1. CEA-608 para tradução CEA-708
2. CeA-608/CEA-708 passa (incorporado em mensagens SEI de correntes elementares H.264, ou transportados como dados auxiliares em ficheiros MXF)
3. SCC
4. Texto Cronometrado SMPTE (a partir da fonte CEA-608 por SMPTE RP2052; incluindo a criação de ficheiros DFXP)
5. Ficheiro de subtítulo SRT
6. Fluxos de legendas DVB

> [!NOTE]
> Nem todos os formatos de saída acima são suportados para entrega através do streaming nos Serviços De Mídia Azure.

## <a name="known-issues"></a>Problemas conhecidos

Se o seu vídeo de entrada não contiver legendas fechadas, o Ativo de saída ainda conterá um ficheiro TTML vazio. 

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de apoio
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


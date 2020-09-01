---
title: Media Encoder Premium Workflow formatos e codecs Microsoft Docs
description: Este tópico dá uma visão geral dos formatos e codecs de formatos e codecs de fluxo de trabalho premium media Encoder
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
ms.openlocfilehash: 38e794ce9e5a1cdc5aafeb1645f7a2b8c95520e2
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268344"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium Workflow formatos e codecs

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> O processador de mídia Media Encoder Premium Workflow discutido neste tópico não está disponível na China. 

Este documento contém uma lista de formatos e códigos de ficheiros de entrada e saída que são suportados pela versão de pré-visualização pública do codificador **de fluxo de trabalho Media Encoder Premium.**

[Formatos e Codecs de entrada de fluxo de trabalho premium media Encoder Premium](#input_formats)

Formatos e Codecs de saída de fluxo de trabalho premium de media Encoder Premium

**O Fluxo de Trabalho Premium Media Encoder** suporta legendas fechadas descritas [nesta](#closed_captioning) secção. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Formatos e Codecs de entrada de fluxo de trabalho premium media Encoder Premium

A secção seguinte lista os formatos de códigos e ficheiros que este processador de mídia suporta como entrada.

### <a name="input-containerfile-formats"></a>Contentor de Entrada/Formatos de Ficheiro

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Fluxos de transporte MPEG-2
* Streams de Programas MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (8bit/10bit descomprimido)

### <a name="input-video-codecs"></a>Codecs de Vídeo de Entrada

* AVC 8-bits/10-bits, até 4:2:2, incluindo AVCIntra
* Avid DNxHD (no MXF)
* DVCPro/DVCProHD (no MXF)
* PERFIL HEVC/H.265, Principal e Principal 10
* JPEG2000
* MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Codecs de Entrada de Áudio

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
* MPEG Camada 2
* MP3 (MPEG-1 Camada de Áudio 3)
* Áudio de Suporte de Dados do Windows
* WAV/PCM

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Formatos e Codecs de saída de fluxo de trabalho premium de media Encoder Premium

A secção seguinte lista os formatos de códigos e ficheiros suportados como saída deste processador de mídia.

### <a name="output-containerfile-formats"></a>Formatos de contentores/ficheiros de saída

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM e AS02)
* DPP (incluindo AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (8bit/10bit descomprimido)
* Formato de ficheiro de streaming suave (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Códigos de vídeo de saída

* AVC (H.264; 8-bit; até High Profile, Nível 5.2; 4K Ultra HD; AVC Intra)
* Avid DNxHD (no MXF)
* DVCPro/DVCProHD (no MXF)
* MPEG-2 (até 422 Perfil e Alto Nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Windows Media Video/VC-1
* Criação de miniatura JPEG
* HEVC (H.265; 8 bit e 10 bits, Perfil Principal e Principal 10)


### <a name="output-audio-codecs"></a>Codecs de áudio de saída

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) até 7.1
* AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
* MPEG Camada 2
* MP3 (MPEG-1 Camada de Áudio 3)
* Áudio de Suporte de Dados do Windows

>[!NOTE]
>Se codificar para Dolby® Digital (AC3), a saída só pode ser escrita num ficheiro MP4 ISO.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Apoio à legenda fechada

No que diz sobre ingestão, **o fluxo de trabalho premium da Media Encoder** suporta:

1. Ficheiros SCC
2. Ficheiros SMPTE-TT
3. CEA-608/CEA-708 – transportado como dados de utilizador (mensagens SEI de streams elementares H.264, ATSC/53, SCTE20) ou transportados como dados auxiliares em ficheiros MXF/GXF
4. Ficheiros de legendas STL

Na saída, estão disponíveis as seguintes opções:

1. Tradução CEA-608 para CEA-708
2. Passe CEA-608/CEA-708 (incorporado em mensagens SEI de fluxos elementares H.264, ou transportado como dados auxiliares em ficheiros MXF)
3. SCC
4. Texto Cronometrado SMPTE (a partir da fonte CEA-608 por SMPTE RP2052; incluindo criação de ficheiros DFXP)
5. Ficheiro de legendagem SRT
6. Streams de legendas DVB

> [!NOTE]
> Nem todos os formatos de saída acima são suportados para entrega através do streaming nos Azure Media Services.

## <a name="known-issues"></a>Problemas conhecidos

Se o seu vídeo de entrada não contiver legendas fechadas, o Ativo de saída ainda conterá um ficheiro TTML vazio. 

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido de apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


---
title: Formatos e codecs de Media Encoder Premium Workflow | Microsoft Docs
description: Este tópico fornece uma visão geral dos codecs e formatos de formatos de Media Encoder Premium Workflow
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
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792593"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formatos e codecs de Media Encoder Premium Workflow

> [!NOTE]
> Media Encoder Premium Workflow processador de mídia discutido neste tópico não está disponível na China. 

Este documento contém uma lista de formatos de arquivo de entrada e saída e codecs que são suportados pela versão de visualização pública do codificador de **Media Encoder Premium Workflow** .

[Media Encoder Premium Workflow formatos de entrada e codecs](#input_formats)

Formatos e codecs de saída Media Encoder Premium Workflow

[O](#closed_captioning) **Media Encoder Premium Workflow** dá suporte à legenda codificada descrita nesta seção. 

## <a id="input_formats"></a>Media Encoder Premium Workflow formatos de entrada e codecs

A seção a seguir lista os codecs e formatos de arquivo aos quais esse processador de mídia dá suporte como entrada.

### <a name="input-containerfile-formats"></a>Formatos de contêiner/arquivo de entrada

* Adobe® Flash® F4V
* 377M MXF/SMPTE
* GXF
* Fluxos de transporte MPEG-2
* Fluxos de programa MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (8 bits/descompactado não compactados)

### <a name="input-video-codecs"></a>Codecs de vídeo de entrada

* AVC de 8 bits/10 bits, até 4:2:2, incluindo AVCIntra
* Ávido DNxHD (em MXF)
* DVCPro/DVCProHD (em MXF)
* Perfil de HEVC/H. 265, Main e Main 10
* JPEG2000
* MPEG-2 (até 422 de perfil e alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Codecs de áudio de entrada

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5,1)
* Camada 2 MPEG
* MP3 (MPEG-1 áudio camada 3)
* Áudio do Windows Media
* WAV/PCM

## <a id="output_format"></a>Formatos e codecs de saída Media Encoder Premium Workflow

A seção a seguir lista os codecs e formatos de arquivo que têm suporte como saída desse processador de mídia.

### <a name="output-containerfile-formats"></a>Formatos de contêiner/arquivo de saída

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM e AS02)
* DPP (incluindo AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (8 bits/descompactado não compactados)
* Formato de arquivo de Smooth Streaming (PIFF 1,3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Codecs de vídeo de saída

* AVC (H. 264; 8 bits; até o alto perfil, nível 5,2; 4K ultra HD; AVC intra)
* Ávido DNxHD (em MXF)
* DVCPro/DVCProHD (em MXF)
* MPEG-2 (até 422 de perfil e alto nível; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® e D10)
* MPEG-1
* Windows Media Video/VC-1
* Criação de miniatura JPEG
* HEVC (H. 265; perfil de 8 bits e 10 bits, principal e principal)


### <a name="output-audio-codecs"></a>Codecs de áudio de saída

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) até 7,1
* AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5,1)
* Camada 2 MPEG
* MP3 (MPEG-1 áudio camada 3)
* Áudio do Windows Media

>[!NOTE]
>Se você codificar para Dolby® Digital (AC3), a saída só poderá ser gravada em um arquivo. MP4 ISO.

## <a id="closed_captioning"></a>Suporte para Legendagem oculta

Ao ingerir, **Media Encoder Premium Workflow** dá suporte a:

1. Arquivos SCC
2. Arquivos SMPTE-TT
3. CEA-608/CEA-708 – transportados como dados do usuário (mensagens SEI de fluxos elementares de H. 264, ATSC/53, SCTE20) ou transportados como dados complementares em arquivos MXF/GXF
4. Arquivos de subtítulo da STL

Na saída, as seguintes opções estão disponíveis:

1. Conversão de CEA-608 para CEA-708
2. O CEA-608/CEA-708 passa (inserido em mensagens de SEI de fluxos elementares de H. 264, ou são transportados como dados complementares em arquivos MXF)
3. SCC
4. Texto com tempo de SMPTE (da origem CEA-608 por SMPTE RP2052; incluindo a criação de arquivo DFXP)
5. Arquivo de subtítulo do SRT
6. Fluxos de subtítulo DVB

> [!NOTE]
> Nem todos os formatos de saída acima têm suporte para entrega por meio de streaming nos serviços de mídia do Azure.

## <a name="known-issues"></a>Problemas conhecidos

Se o vídeo de entrada não contiver legendas codificadas, o ativo de saída ainda conterá um arquivo TTML vazio. 

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um tíquete de suporte navegando até [nova solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


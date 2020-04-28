---
title: Visão geral dinâmica da embalagem da Azure Media Services Microsoft Docs
description: Estes artigos dão uma visão geral da embalagem dinâmica da Microsoft Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74901193"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 3](../latest/dynamic-packaging-overview.md)
> * [Versão 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

O Microsoft Azure Media Services pode ser utilizado para fornecer muitos formatos de ficheiros de fonte de mídia, formatos de streaming de mídia e formatos de proteção de conteúdos para uma variedade de tecnologias de cliente (por exemplo, iOS, XBOX, Silverlight, Windows 8). Estes clientes entendem diferentes protocolos, por exemplo o iOS requer um formato V4 de Streaming AO Vivo (HLS) http e Silverlight e Xbox requerem Smooth Streaming. Se tiver um conjunto de ficheiros MP4 (multibituantes) de bitrate adaptativo (multibitado) (ISO Base Media 14496-12) ou um conjunto de ficheiros adaptáveis de streaming suave que pretende servir a clientes que compreendam mPEG DASH, HLS ou Smooth Streaming, deverá aproveitar a embalagem dinâmica dos Media Services.

Com a embalagem dinâmica tudo o que precisa é criar um ativo que contenha um conjunto de ficheiros MP4 bitrate adaptativos ou ficheiros de streaming de bitrate adaptativo. Em seguida, com base no formato especificado no pedido de manifesto ou fragmento, o servidor de Streaming On-Demand irá garantir que recebe o fluxo no protocolo que escolheu. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

O diagrama que se segue mostra o fluxo de trabalho tradicional de codificação e embalagem estática.

![Codificação estática](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama seguinte mostra o fluxo de trabalho dinâmico da embalagem.

![Codificação dinâmica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Cenário comum

1. Faça upload de um ficheiro de entrada (chamado ficheiro mezanino). Por exemplo, H.264, MP4 ou WMV (para a lista de formatos suportados ver [Formatos Suportados pela Norma Media Encoder](media-services-media-encoder-standard-formats.md).
2. Codifique o seu ficheiro mezanino a conjuntos de bitrate adaptativo H.264 MP4.
3. Publique o ativo que contém o BITRATE BIT4 adaptativo definido pela criação do Localizador On-Demand.
4. Construa os URLs de streaming para aceder e transmitir o seu conteúdo.

## <a name="preparing-assets-for-dynamic-streaming"></a>Preparação de ativos para streaming dinâmico

Para preparar o seu ativo para o streaming dinâmico, tem as seguintes opções:

- [Faça upload de um ficheiro principal.](media-services-dotnet-upload-files.md)
- [Utilize o codificador De código Media Encoder Standard para produzir conjuntos de bitrate adaptativo H.264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Transmita o seu conteúdo.](media-services-deliver-content-overview.md)

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codificadores de áudio suportados por embalagens dinâmicas

A Dynamic Packaging suporta ficheiros MP4, que contêm áudio codificado com [AAC (AAC-LC,](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 ou E-AC3), Dolby Atmos, ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless). O streaming de conteúdo do Dolby Atmos é suportado para normas como o protocolo MPEG-DASH com formato de streaming comum (CSF) ou formato de aplicação de mídia comum (CMAF) fragmentado MP4, e via HTTP Live Streaming (HLS) com CMAF.

> [!NOTE]
> A Dynamic Packaging não suporta ficheiros que contenham áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (é um codec legado).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


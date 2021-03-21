---
title: Visão geral da embalagem dinâmica da Azure Media Services | Microsoft Docs
description: Estes artigos dão uma visão geral da embalagem dinâmica da Microsoft Azure Media Services.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d2394cba157a7049e3b63b09fda9e018f0a0b32f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013623"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecione a versão dos Serviços de Media que está a utilizar:"]
> * [Versão 3](../latest/dynamic-packaging-overview.md)
> * [Versão 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

O Microsoft Azure Media Services pode ser utilizado para fornecer muitos formatos de ficheiros de origem de mídia, formatos de streaming de mídia e formatos de proteção de conteúdos para uma variedade de tecnologias de clientes (por exemplo, iOS, XBOX, Silverlight, Windows 8). Estes clientes compreendem diferentes protocolos, por exemplo, o iOS requer um formato HTTP Live Streaming (HLS) V4 e Silverlight e Xbox requerem Smooth Streaming. Se tiver um conjunto de ficheiros adaptáveis de bitrate (multi-bitrate) MP4 (ISO Base Media 14496-12) ou um conjunto de ficheiros de streaming suave adaptável que pretende servir a clientes que compreendam MPEG DASH, HLS ou Smooth Streaming, deverá aproveitar a embalagem dinâmica dos Media Services.

Com todas as embalagens dinâmicas, precisa de criar um ativo que contenha um conjunto de ficheiros mp4 de bitrate adaptativo ou ficheiros de streaming suave de bitrate adaptativo. Em seguida, com base no formato especificado no pedido manifesto ou fragmento, o servidor de Streaming On-Demand garantirá que recebe o fluxo no protocolo que escolheu. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

O diagrama a seguir mostra o fluxo de trabalho de embalagem tradicional e estático.

![Codificação estática](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama a seguir mostra o fluxo de trabalho dinâmico da embalagem.

![Codificação dinâmica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Cenário comum

1. Faça o upload de um ficheiro de entrada (chamado de ficheiro mezanino). Por exemplo, H.264, MP4 ou WMV (para a lista de formatos suportados ver [Formatos Suportados pela Norma Media Encoder](media-services-media-encoder-standard-formats.md).
2. Codificar o seu ficheiro mezanino para conjuntos de bitrate adaptativos H.264 MP4.
3. Publique o ativo que contém o conjunto de BITRATE adaptativo MP4, criando o Localizador On-Demand.
4. Construa os URLs de streaming para aceder e transmitir o seu conteúdo.

## <a name="preparing-assets-for-dynamic-streaming"></a>Preparar ativos para o streaming dinâmico

Para preparar o seu ativo para o streaming dinâmico, tem as seguintes opções:

- [Faça o upload de um ficheiro principal.](media-services-dotnet-upload-files.md)
- [Utilize o codificadores Media Encoder Standard para produzir conjuntos de bitrate adaptativos H.264 MP4](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Transmita o seu conteúdo](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio suportados por embalagens dinâmicas

A Dynamic Packaging suporta ficheiros MP4, que contêm áudio codificado com [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(AC-3 ou E-AC3 Melhorado), Dolby Atmos ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless). O streaming de conteúdo do Dolby Atmos é suportado para padrões como o protocolo MPEG-DASH com o Formato Comum de Streaming (CSF) ou o Formato Comum de Aplicação de Mídia (CMAF) fragmentado MP4, e via HTTP Live Streaming (HLS) com CMAF.

> [!NOTE]
> A Dynamic Packaging não suporta ficheiros que contenham áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (é um codec legado).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

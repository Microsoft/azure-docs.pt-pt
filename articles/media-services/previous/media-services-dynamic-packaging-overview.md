---
title: Visão geral do empacotamento dinâmico dos serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de Serviços de Mídia do Microsoft Azure empacotamento dinâmico.
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
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901193"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

> [!div class="op_single_selector" title1="Selecione a versão dos serviços de mídia que você está usando:"]
> * [Versão 3](../latest/dynamic-packaging-overview.md)
> * [Versão 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Serviços de Mídia do Microsoft Azure pode ser usado para fornecer vários formatos de arquivo de origem de mídia, formatos de streaming de mídia e formatos de proteção de conteúdo para uma variedade de tecnologias de cliente (por exemplo, iOS, XBOX, Silverlight, Windows 8). Esses clientes entendem protocolos diferentes, por exemplo, o iOS requer um formato de HTTP Live Streaming (HLS) v4 e o Silverlight e o Xbox exigem Smooth Streaming. Se você tiver um conjunto de arquivos MP4 (mídia base ISO 14496-12) de taxa de bits adaptável (múltiplas taxas de bits) ou um conjunto de arquivos de Smooth Streaming de taxa de bits adaptável que você deseja fornecer aos clientes que entendem MPEG DASH, HLS ou Smooth Streaming, você deverá aproveitar a mídia Empacotamento dinâmico de serviços.

Com o empacotamento dinâmico, é necessário criar um ativo que contenha um conjunto de arquivos MP4 de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Em seguida, com base no formato especificado no manifesto ou na solicitação de fragmento, o servidor de streaming sob demanda garantirá que você receba o fluxo no protocolo escolhido. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

O diagrama a seguir mostra a codificação tradicional e o fluxo de trabalho de empacotamento estático.

![Codificação estática](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama a seguir mostra o fluxo de trabalho de empacotamento dinâmico.

![Codificação dinâmica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Cenário comum

1. Carregue um arquivo de entrada (chamado de arquivo de mezanino). Por exemplo, H. 264, MP4 ou WMV (para obter a lista de formatos com suporte, consulte [formatos com suporte pelo Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Codifique seu arquivo de mezanino para conjuntos de taxa de bits adaptável MP4 H. 264.
3. Publique o ativo que contém o conjunto de MP4 de taxa de bits adaptável criando o localizador sob demanda.
4. Crie as URLs de streaming para acessar e transmitir seu conteúdo.

## <a name="preparing-assets-for-dynamic-streaming"></a>Preparando ativos para streaming dinâmico

Para preparar seu ativo para streaming dinâmico, você tem as seguintes opções:

- [Carregar um arquivo mestre](media-services-dotnet-upload-files.md).
- [Use o codificador Media Encoder Standard para produzir conjuntos de taxa de bits adaptável MP4 H. 264](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Transmita seu conteúdo](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio com suporte do empacotamento dinâmico

O empacotamento dinâmico dá suporte a arquivos MP4, que contêm áudio codificado com [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 ou e-AC3), Dolby Atmos ou [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD lossless). O streaming do conteúdo Dolby Atmos tem suporte para padrões como o protocolo MPEG-DASH com o formato de streaming comum (CSF) ou o MP4 fragmentado do CMAF (formato de aplicativo de mídia comum) e via HTTP Live Streaming (HLS) com CMAF.

> [!NOTE]
> O empacotamento dinâmico não dá suporte a arquivos que contêm áudio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (é um codec herdado).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


---
title: Descrição geral de empacotamento dinâmico dos serviços de multimédia do Azure | Documentos da Microsoft
description: O tópico dá e uma descrição geral do empacotamento dinâmico.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 15599beb47b7f6e72b89e7776196de8e6b94844f
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189177"
---
# <a name="dynamic-packaging"></a>Empacotamento dinâmico

Serviços de multimédia do Microsoft Azure podem ser utilizados para disponibilizar formatos de muitos arquivos de origem do suporte de dados, suportes de dados em fluxo em formatos, e a proteção de conteúdo formata a uma variedade de tecnologias de cliente (por exemplo, iOS, XBOX, Silverlight, Windows 8). Estes clientes compreender protocolos diferentes, por exemplo iOS requer um formato de V4 HTTP Live Streaming (HLS) e Silverlight e o Xbox precisam de transmissão em fluxo uniforme. Se tiver um conjunto de velocidade de transmissão adaptável (múltipla) MP4 ou um conjunto de arquivos transmissão em fluxo uniforme de velocidade de transmissão adaptável que deve servir para clientes que compreender MPEG DASH, HLS ou transmissão em fluxo uniforme de ficheiros (ISO Base Media 14496-12), deve tirar vantagem do suporte de dados Serviços de empacotamento dinâmico.

Com o empacotamento dinâmico, tudo o que precisa é criar um elemento que contém um conjunto de ficheiros MP4 de velocidade de transmissão adaptável ou ficheiros de transmissão em fluxo uniforme de velocidade de transmissão adaptável. Em seguida, com base no formato especificado no pedido de manifesto ou fragmento, a transmissão em fluxo a pedido em servidor irá garantir que recebe o fluxo no protocolo que escolheu. Como resultado, só tem de armazenar e pagar pelos ficheiros num único formato de armazenamento e os Media Services irão compilar e disponibilizar a resposta adequada com base nos pedidos de um cliente.

O diagrama seguinte mostra a codificação tradicional e o fluxo de trabalho de empacotamento estáticos.

![Codificação estático](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

O diagrama seguinte mostra o fluxo de trabalho de empacotamento dinâmico.

![Codificação dinâmico](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Cenário comum
1. Carregar um ficheiro de entrada (chamado de um ficheiro de mezanino). Por exemplo, H.264, MP4 ou WMV (para obter a lista dos formatos suportados, consulte [formatos suportados pelo Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Codificar o ficheiro de mezanino para os conjuntos H.264 MP4 de velocidade de transmissão adaptável.
3. Publique o elemento que contém a velocidade de transmissão adaptável MP4 definido através da criação do localizador a pedido.
4. Crie os URLs de transmissão em fluxo para aceder e transmitir o seu conteúdo.

## <a name="preparing-assets-for-dynamic-streaming"></a>A preparar a ativos de dinâmica de transmissão em fluxo
Para preparar o seu recurso de dinâmica de transmissão em fluxo tem as seguintes opções:

- [Carregar um ficheiro mestre](media-services-dotnet-upload-files.md).
- [Usar o Media Encoder Standard codificador para produzir os conjuntos H.264 MP4 de velocidade de transmissão adaptável](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Stream seu conteúdo](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Codecs de áudio suportado pelo empacotamento dinâmico

Empacotamento dinâmico suporta ficheiros MP4 (ou Smooth Streaming ficheiros) que contêm áudio codificado com [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 HE-AAC), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (3 de AC avançada ou E AC3), ou [ DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, LBR de DTS, DTS HD, HD DTS sem perdas).

> [!Note]
> Empacotamento dinâmico não suporta ficheiros que contêm [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) áudio (AC3) (é um codec herdado).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


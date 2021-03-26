---
title: Jogadores de mídia para visão geral dos Serviços de Mídia
description: Que media-jogadores posso usar com a Azure Media Services? Azure Media Player, Shaka e Video.js até agora.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 47ef56a770ca9965cef3e50630be4b69342a038d
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559864"
---
# <a name="media-players-for-media-services"></a>Media players for Media Services

Você pode usar vários media players com Media Services.

## <a name="azure-media-player"></a>Media Player do Azure

Azure Media Player é um leitor de vídeo para uma grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões da indústria, tais como HTML5, Extensões de Fonte de Mídia (MSE) e Extensões de Mídia Encriptadas (EME) para proporcionar uma experiência de streaming adaptativa enriquecida. Quando estes padrões não estão disponíveis num dispositivo ou num browser, o Azure Media Player utiliza o Flash e o Silverlight como tecnologia de retorno. Qualquer que seja a tecnologia de reprodução utilizada, os desenvolvedores têm uma interface JavaScript unificada para aceder a APIs. Os conteúdos servidos pela Azure Media Services podem ser reproduzidos através de uma vasta gama de dispositivos e navegadores sem qualquer esforço extra.

Consulte a documentação do [Azure Media Player](../azure-media-player/azure-media-player-overview.md).

## <a name="shaka"></a>Rio Shaka

Shaka Player é uma biblioteca JavaScript de código aberto para meios adaptativos. Reproduz formatos de mídia adaptativo (como DASH e HLS) num browser, sem usar plugins ou Flash. Em vez disso, o Jogador Shaka utiliza as extensões de origem de mídia abertas e extensões de mídia encriptadas.

Veja [como utilizar o jogador Shaka com a Azure Media Services](how-to-shaka-player.md).

## <a name="videojs"></a>Video.js

Video.js é um leitor de vídeo que reproduz formatos de mídia adaptativos (como o DASH e o HLS) num browser. Video.js utiliza as extensões de MediaSource e extensões de mídia encriptadas. Suporta a reprodução de vídeo em desktops e dispositivos móveis. A sua documentação oficial pode ser consultada em https://docs.videojs.com/ .

Veja [como utilizar o jogador Video.js com a Azure Media Services](how-to-video-js-player.md).


## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](../azure-media-player/azure-media-player-quickstart.md)
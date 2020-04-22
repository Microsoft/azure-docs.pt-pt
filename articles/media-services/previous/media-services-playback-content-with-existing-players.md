---
title: Utilize os jogadores existentes para reproduzir o seu conteúdo - Azure / Microsoft Docs
description: Este artigo lista os jogadores existentes que pode utilizar para reproduzir o seu conteúdo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2d3c22e17c37bc46c16a9cc80eb3cf4b9ec93ecf
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686929"
---
# <a name="playing-your-content-with-existing-players"></a>Reproduzir o conteúdo com os leitores existentes
A Azure Media Services suporta muitos formatos de streaming populares, tais como Smooth Streaming, HTTP Live Streaming e MPEG-Dash. Este tópico aponta-o para os jogadores existentes que pode usar para testar os seus fluxos.

### <a name="the-azure-portal-media-services-content-player"></a>O leitor de conteúdos do portal Azure Media Services
O portal **Azure** fornece um leitor de conteúdos que pode utilizar para testar o seu vídeo.

Clique no vídeo desejado (certifique-se de que foi [publicado)](media-services-portal-publish.md)e clique no botão **Reproduzir** na parte inferior do portal.

São aplicáveis algumas considerações:

* O **LEITOR DE CONTEÚDOS DE MEDIA SERVICES** reproduz a partir do ponto final de transmissão em fluxo predefinido. Se pretender reproduzir a partir de um ponto final de transmissão em fluxo não predefinido, utilize outro leitor. Por exemplo, [Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Media Player do Azure

Utilize o [Azure Media Player](https://aka.ms/azuremediaplayer) para reproduzir o seu conteúdo (claro ou protegido) em qualquer um dos seguintes formatos:

* Transmissão em Fluxo Uniforme
* MPEG DASH
* HLS
* MP4 progressista

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady com Token

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Jogadores do DASH

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Outros
Para testar URLs HLS também pode utilizar:

* **Safari** em um dispositivo iOS ou
* **3ivx HLS Player** no Windows.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

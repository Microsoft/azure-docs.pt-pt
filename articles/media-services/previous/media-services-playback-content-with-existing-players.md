---
title: Utilize os jogadores existentes para reproduzir o seu conteúdo - Azure | Microsoft Docs
description: Este artigo lista os jogadores existentes que pode utilizar para reproduzir o seu conteúdo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ce773adace1baf6db8f1b747643ef0ffdc56a97c
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008297"
---
# <a name="playing-your-content-with-existing-players"></a>Reproduzir o conteúdo com os leitores existentes

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

A Azure Media Services suporta muitos formatos de streaming populares, tais como Smooth Streaming, HTTP Live Streaming e MPEG-Dash. Este tópico aponta para os jogadores existentes que pode usar para testar os seus streams.

## <a name="the-azure-portal-media-services-content-player"></a>O leitor de conteúdos do portal Azure Media Services

O portal **Azure** fornece um leitor de conteúdos que pode usar para testar o seu vídeo.

Clique no vídeo pretendido (certifique-se de que foi [publicado)](media-services-portal-publish.md)e clique no botão **Reproduzir** na parte inferior do portal.

São aplicáveis algumas considerações:

* O **LEITOR DE CONTEÚDOS DE MEDIA SERVICES** reproduz a partir do ponto final de transmissão em fluxo predefinido. Se pretender reproduzir a partir de um ponto final de transmissão em fluxo não predefinido, utilize outro leitor. Por exemplo, [Azure Media Player](https://aka.ms/azuremediaplayer).

### <a name="azure-media-player"></a>Media Player do Azure

Utilize [o Azure Media Player](https://aka.ms/azuremediaplayer) para reproduzir o seu conteúdo (claro ou protegido) em qualquer um dos seguintes formatos:

* Transmissão em Fluxo Uniforme
* MPEG DASH
* HLS
* MP4 progressivo

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady com Token

[http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html](http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html)

### <a name="dash-players"></a>Jogadores do DASH

[jogador traço](http://players.akamai.com/players/dashjs)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Outro

Para testar URLs HLS também pode utilizar:

* **Safari** em um dispositivo iOS ou
* **3ivx HLS Player** no Windows.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

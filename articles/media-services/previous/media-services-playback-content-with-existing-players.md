---
title: Usar players existentes para reproduzir seu conteúdo-Azure | Microsoft Docs
description: Este tópico lista os players existentes que você pode usar para reproduzir o conteúdo.
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
ms.openlocfilehash: 0da8ce396ce07b7c9859fcfccb5cb524c0ce21c1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "69015300"
---
# <a name="playing-your-content-with-existing-players"></a>Jogando seu conteúdo com os players existentes
Os serviços de mídia do Azure dão suporte a vários formatos populares de streaming, como Smooth Streaming, HTTP Live Streaming e MPEG-Dash. Este tópico aponta para os players existentes que você pode usar para testar seus fluxos.

### <a name="the-azure-portal-media-services-content-player"></a>O portal do Azure player de conteúdo dos serviços de mídia
O portal **do Azure** fornece um player de conteúdo que você pode usar para testar seu vídeo.

Clique no vídeo desejado (verifique se ele foi [publicado](media-services-portal-publish.md)) e clique no botão **reproduzir** na parte inferior do Portal.

São aplicáveis algumas considerações:

* O **LEITOR DE CONTEÚDOS DE MEDIA SERVICES** reproduz a partir do ponto final de transmissão em fluxo predefinido. Se pretender reproduzir a partir de um ponto final de transmissão em fluxo não predefinido, utilize outro leitor. Por exemplo, [player de mídia do Azure](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Media Player do Azure

Use [player de mídia do Azure](https://aka.ms/azuremediaplayer) para reproduzir seu conteúdo (claro ou protegido) em qualquer um dos seguintes formatos:

* Transmissão em Fluxo Uniforme
* MPEG DASH
* HLS
* MP4 progressivo

### <a name="flash-player"></a>Flash Player

#### <a name="aes-encrypted-with-token"></a>Criptografado por AES com token

[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

#### <a name="playready-with-token"></a>PlayReady com token

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Jogadores de DASH

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Outros
Para testar as URLs do HLS, você também pode usar:

* **Safari** em um dispositivo IOS ou
* **3ivx HLS Player** no Windows.

## <a name="developing-video-players"></a>Desenvolvendo players de vídeo
Para obter informações sobre como desenvolver seus próprios jogadores, consulte [desenvolvendo players de vídeo](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png

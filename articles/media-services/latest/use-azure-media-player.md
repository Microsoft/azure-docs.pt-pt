---
title: Reprodução com Azure Media Player - Azure / Microsoft Docs
description: O Azure Media Player é um leitor de vídeo sonoro construído para reproduzir conteúdo sonoro da Microsoft Azure Media Services numa grande variedade de navegadores e dispositivos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: 63b05662e16dbcfd999788aa39c6c37eb12cf2c7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530410"
---
# <a name="playback-with-azure-media-player"></a>Reprodução com Azure Media Player

O Azure Media Player é um leitor de vídeo sonoro construído para reproduzir conteúdo sonoro da Microsoft Azure Media Services numa grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões da indústria, tais como HTML5, Extensões de Fonte de Mídia (MSE) e Extensões de Mídia Encriptadas (EME) para proporcionar uma experiência de streaming adaptativa enriquecida. Quando estes padrões não estão disponíveis num dispositivo ou num browser, o Azure Media Player utiliza o Flash e o Silverlight como tecnologia de retorno. Independentemente da tecnologia de reprodução utilizada, os desenvolvedores terão uma interface JavaScript unificada para aceder a APIs. Isto permite que os conteúdos servidos pela Azure Media Services sejam reproduzidos em uma vasta gama de dispositivos e navegadores sem qualquer esforço extra.

O Microsoft Azure Media Services permite que os conteúdos sejam servidos com formatos de streaming HLS, DASH, Smooth Streaming para reproduzir conteúdo. O Azure Media Player tem em conta estes vários formatos e reproduz automaticamente o melhor link baseado nas capacidades da plataforma/navegador. Os Serviços de Mídia também permitem encriptação dinâmica de ativos com encriptação PlayReady ou encriptação de envelope aES-128 bit. O Azure Media Player permite a desencriptação de conteúdo encriptado PlayReady e AES-128 quando devidamente configurado. 

> [!NOTE]
> A reprodução HTTPS é necessária para conteúdo encriptado Widevine.

## <a name="use-azure-media-player-demo-page"></a>Use a página de demonstração do Azure Media Player

### <a name="start-using"></a>Comece a usar

Pode utilizar a [página de demonstração do Azure Media Player](https://aka.ms/azuremediaplayer) para reproduzir amostras do Azure Media Services ou do seu próprio fluxo.  

Para reproduzir um novo vídeo, cole um URL diferente e prima **Update**.

Para configurar várias opções de reprodução (por exemplo, tecnologia, linguagem ou encriptação), prima **Opções Avançadas**.

![Media Player do Azure](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorize diagnósticos de um fluxo de vídeo

Pode utilizar a [página de demonstração do Azure Media Player](https://aka.ms/azuremediaplayer) para monitorizar os diagnósticos de um stream de vídeo. 

![Diagnósticos do Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Configurar o Azure Media Player no seu HTML

O Azure Media Player é fácil de configurar. Bastam alguns momentos para obter a reprodução básica de conteúdos de mídia na sua conta de Media Services. Consulte [a documentação do Azure Media Player](https://aka.ms/ampdocs) para obter mais detalhes sobre como configurar e configurar o Azure Media Player. 

## <a name="additional-notes"></a>Notas adicionais

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Próximos passos

- [Azure Media Player documentation](https://aka.ms/ampdocs) (Documentação do Leitor de Multimédia do Azure)
- [Exemplos do Leitor de Multimédia do Azure](https://aka.ms/ampsamples)

---
title: Visão geral do jogador de mídia azure
description: Saiba mais sobre o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726523"
---
# <a name="azure-media-player-overview"></a>Azure Media Player overview (Descrição geral do Leitor de Multimédia do Azure) #

O Azure Media Player é um leitor de vídeo web construído para reproduzir conteúdos de media da [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) numa grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões industriais, tais como HTML5, Extensões de Fonte de Media (MSE) e Extensões de Meios Encriptados (EME) para proporcionar uma experiência de streaming adaptável enriquecida.  Quando estes padrões não estão disponíveis num dispositivo ou num browser, o Azure Media Player utiliza flash e Silverlight como tecnologia de recuo. Independentemente da tecnologia de reprodução utilizada, os desenvolvedores terão uma interface JavaScript unificada para aceder a APIs.  Isto permite que os conteúdos servidos pela Azure Media Services sejam reproduzidos através de uma vasta gama de dispositivos e navegadores sem qualquer esforço extra.

O Microsoft Azure Media Services permite que os conteúdos sejam servidos com formatos de streaming DASH, Smooth Streaming e HLS para reproduzir conteúdos. O Azure Media Player tem em conta estes vários formatos e reproduz automaticamente o melhor link com base nas capacidades da plataforma/navegador. O Microsoft Azure Media Services também permite encriptação dinâmica de ativos com encriptação comum (PlayReady ou Widevine) ou encriptação de envelopes aES-128 bit. O Azure Media Player permite a desencriptação do conteúdo encriptado da BitReady e AES-128 quando devidamente configurado.  Consulte a secção [de Conteúdo Protegido](azure-media-player-protected-content.md) para saber como configurar isto.

Para solicitar novas funcionalidades, forneça ideias ou feedback, por favor envie-se ao [UserVoice para O Leitor de Mídia Azure](https://aka.ms/ampuservoice). Se tiver problemas específicos, questionar ou encontrar insetos, deixe-nos uma linha em ampinfo@microsoft.com.

[Inscreva-se](https://aka.ms/ampsignup) para nunca perder um lançamento e para ficar atualizado com as mais recentes que o Azure Media Player tem para oferecer.

> [!NOTE]
> Por favor, note que o Azure Media Player apenas suporta streams de meios da Azure Media Services.

## <a name="license"></a>Licença ##

O Azure Media Player está licenciado e sujeito aos termos descritos nos Termos de Licença de Software da Microsoft para o Azure Media Player. Consulte o ficheiro da licença para obter os termos [completos.](azure-media-player-license.md) Consulte a [Declaração de Privacidade](https://www.microsoft.com/en-us/privacystatement/default.aspx) para mais informações.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)
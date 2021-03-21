---
title: Visão geral do Azure Media Player
description: Saiba mais sobre o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 9b99e24e47da69aa0f081e056230f4b0c1aa6bb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321736"
---
# <a name="azure-media-player-overview"></a>Descrição geral do Leitor de Multimédia do Azure #

Azure Media Player é um leitor de vídeo sonoro que reproduz conteúdos de mídia da [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) numa grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões da indústria, tais como HTML5, Extensões de Fonte de Mídia (MSE) e Extensões de Mídia Encriptadas (EME) para proporcionar uma experiência de streaming adaptativa enriquecida.  Quando estes padrões não estão disponíveis num dispositivo ou num browser, o Azure Media Player utiliza o Flash e o Silverlight como tecnologia de retorno. Independentemente da tecnologia de reprodução utilizada, os desenvolvedores têm uma interface JavaScript unificada para aceder a APIs, permitindo que os conteúdos servidos pela Azure Media Services sejam reproduzidos através de uma vasta gama de dispositivos e navegadores sem qualquer esforço extra.

O Microsoft Azure Media Services permite que os conteúdos sejam servidos com formatos de streaming DASH, Smooth Streaming e HLS para reproduzir conteúdo. O Azure Media Player tem em conta estes vários formatos e reproduz automaticamente o melhor link baseado nas capacidades da plataforma/navegador. O Microsoft Azure Media Services também fornece encriptação dinâmica de ativos com encriptação comum (PlayReady ou Widevine) ou encriptação de envelope bit AES-128. O Azure Media Player permite a desencriptação de conteúdo encriptado PlayReady e AES-128 quando devidamente configurado.  Para entender como configurar o leitor, consulte a secção [Conteúdo Protegido.](azure-media-player-protected-content.md)

Para solicitar novas funcionalidades, fornecer ideias ou feedback, submeta-as ao [UserVoice para O Azure Media Player](https://aka.ms/ampuservoice). Se tiver e questões específicas, perguntas ou encontrar algum bug, deixe-nos uma linha em ampinfo@microsoft.com .

> [!NOTE]
> Por favor, note que o Azure Media Player apenas suporta streams de meios de comunicação da Azure Media Services.

## <a name="license"></a>Licença ##

O Azure Media Player está licenciado e sujeito aos termos descritos nos Termos de Licença de Software da Microsoft para o Azure Media Player. Consulte [o ficheiro da licença](/legal/azure-media-player/azure-media-player-license) para obter os termos completos. Para mais informações, consulte a [Declaração de Privacidade.](https://www.microsoft.com/en-us/privacystatement/default.aspx)

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)

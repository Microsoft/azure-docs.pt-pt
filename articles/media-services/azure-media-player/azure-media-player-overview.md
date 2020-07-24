---
title: Visão geral do Azure Media Player
description: Saiba mais sobre o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 6f3c173c61e9f6d82a51fcaf7e11cda47eb47c1c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011716"
---
# <a name="azure-media-player-overview"></a>Descrição geral do Leitor de Multimédia do Azure #

O Azure Media Player é um leitor de vídeo sonoro construído para reproduzir conteúdo sonoro da [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) numa grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões da indústria, tais como HTML5, Extensões de Fonte de Mídia (MSE) e Extensões de Mídia Encriptadas (EME) para proporcionar uma experiência de streaming adaptativa enriquecida.  Quando estes padrões não estão disponíveis num dispositivo ou num browser, o Azure Media Player utiliza o Flash e o Silverlight como tecnologia de retorno. Independentemente da tecnologia de reprodução utilizada, os desenvolvedores terão uma interface JavaScript unificada para aceder a APIs.  Isto permite que os conteúdos servidos pela Azure Media Services sejam reproduzidos em uma vasta gama de dispositivos e navegadores sem qualquer esforço extra.

O Microsoft Azure Media Services permite que os conteúdos sejam servidos com formatos de streaming DASH, Smooth Streaming e HLS para reproduzir conteúdo. O Azure Media Player tem em conta estes vários formatos e reproduz automaticamente o melhor link baseado nas capacidades da plataforma/navegador. O Microsoft Azure Media Services também permite encriptação dinâmica de ativos com encriptação comum (PlayReady ou Widevine) ou encriptação de envelopes AES-128 bit. O Azure Media Player permite a desencriptação de conteúdo encriptado PlayReady e AES-128 quando devidamente configurado.  Consulte a secção [Conteúdo Protegido](azure-media-player-protected-content.md) para saber como configurar isto.

Para solicitar novas funcionalidades, fornecer ideias ou feedback, por favor submeta-se ao [UserVoice para O Azure Media Player](https://aka.ms/ampuservoice). Se tiver e questões específicas, perguntas ou encontrar algum bug, deixe-nos uma linha em ampinfo@microsoft.com .

[Inscreva-se](https://aka.ms/ampsignup) para nunca perder um lançamento e para ficar atualizado com as últimas novidades que o Azure Media Player tem para oferecer.

> [!NOTE]
> Por favor, note que o Azure Media Player apenas suporta streams de meios de comunicação da Azure Media Services.

## <a name="license"></a>Licença ##

O Azure Media Player está licenciado e sujeito aos termos descritos nos Termos de Licença de Software da Microsoft para o Azure Media Player. Consulte [o ficheiro da licença](/legal/azure-media-player/azure-media-player-license) para obter os termos completos. Consulte a [Declaração de Privacidade](https://www.microsoft.com/en-us/privacystatement/default.aspx) para mais informações.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)

---
title: Reprodução com Azure Media Player - Azure Microsoft Docs
description: O Azure Media Player é um leitor de vídeo web construído para reproduzir conteúdos de media da Microsoft Azure Media Services numa grande variedade de navegadores e dispositivos.
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
ms.openlocfilehash: d9e42e809443a2dd6cdeb989f692b96d63269f79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673585"
---
# <a name="azure-media-player-overview"></a>Descrição geral do Leitor de Multimédia do Azure

O Azure Media Player é um leitor de vídeo web construído para reproduzir conteúdos de media da Microsoft Azure Media Services numa grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões industriais, tais como HTML5, Extensões de Fonte de Media (MSE) e Extensões de Meios Encriptados (EME) para proporcionar uma experiência de streaming adaptável enriquecida. Quando estes padrões não estão disponíveis num dispositivo ou num browser, o Azure Media Player utiliza flash e Silverlight como tecnologia de recuo. Independentemente da tecnologia de reprodução utilizada, os desenvolvedores terão uma interface JavaScript unificada para aceder a APIs. Isto permite que os conteúdos servidos pela Azure Media Services sejam reproduzidos através de uma vasta gama de dispositivos e navegadores sem qualquer esforço extra.

O Microsoft Azure Media Services permite que os conteúdos sejam servidos com formatos HLS, DASH, Smooth Streaming para reproduzir conteúdos. O Azure Media Player tem em conta estes vários formatos e reproduz automaticamente o melhor link com base nas capacidades da plataforma/navegador. Os Serviços de Media também permitem encriptação dinâmica de ativos com encriptação PlayReady ou encriptação de envelope sonante AES-128. O Azure Media Player permite a desencriptação do conteúdo encriptado da BitReady e AES-128 quando devidamente configurado. 

> [!NOTE]
> A reprodução HTTPS é necessária para conteúdo encriptado da Widevine.

## <a name="use-azure-media-player-demo-page"></a>Use a página de demonstração do Azure Media Player

### <a name="start-using"></a>Começar a usar

Pode utilizar a [página de demonstração do Azure Media Player](https://aka.ms/azuremediaplayer) para reproduzir amostras do Azure Media Services ou do seu próprio fluxo.  

Para reproduzir um novo vídeo, colhe um URL diferente e prima **Update**.

Para configurar várias opções de reprodução (por exemplo, tecnologia, linguagem ou encriptação), prima **Opções Avançadas**.

![Media Player do Azure](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorize diagnósticos de um fluxo de vídeo

Pode utilizar a [página de demonstração do Azure Media Player](https://aka.ms/azuremediaplayer) para monitorizar os diagnósticos de um fluxo de vídeo. 

![Diagnósticos de Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Instale o Azure Media Player no seu HTML

Azure Media Player é fácil de configurar. Basta maquete sem estôver os conteúdos dos media na sua conta de Media Services. Consulte a [documentação do Azure Media Player](https://aka.ms/ampdocs) para obter detalhes sobre como configurar e configurar o Azure Media Player. 

## <a name="additional-notes"></a>Notas adicionais

* A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps"></a>Passos seguintes

- [Azure Media Player documentation](https://aka.ms/ampdocs) (Documentação do Leitor de Multimédia do Azure)
- [Exemplos do Leitor de Multimédia do Azure](https://aka.ms/ampsamples)

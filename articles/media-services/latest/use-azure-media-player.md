---
title: Reprodução com Player de Mídia do Azure-Azure | Microsoft Docs
description: Player de Mídia do Azure é um player de vídeo da Web criado para reproduzir conteúdo de mídia de Serviços de Mídia do Microsoft Azure em uma ampla variedade de navegadores e dispositivos.
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
ms.openlocfilehash: 8f1639a21e3783f587904c2a8085fd2f555ff5b8
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900359"
---
# <a name="azure-media-player-overview"></a>Visão geral de Player de Mídia do Azure

Player de Mídia do Azure é um player de vídeo da Web criado para reproduzir conteúdo de mídia de Serviços de Mídia do Microsoft Azure em uma ampla variedade de navegadores e dispositivos. O Player de Mídia do Azure utiliza padrões do setor, como HTML5, extensões de origem de mídia (MSE) e EME (extensões de mídia criptografada) para fornecer uma experiência de streaming adaptável aprimorada. Quando esses padrões não estão disponíveis em um dispositivo ou em um navegador, o Player de Mídia do Azure usa o flash e o Silverlight como tecnologia de fallback. Independentemente da tecnologia de reprodução usada, os desenvolvedores terão uma interface de JavaScript unificada para acessar APIs. Isso permite que o conteúdo servido pelos serviços de mídia do Azure seja reproduzido em uma ampla gama de dispositivos e navegadores sem nenhum esforço extra.

Serviços de Mídia do Microsoft Azure permite que o conteúdo seja servido com formatos de streaming HLS, DASH Smooth Streaming para reproduzir conteúdo. Player de Mídia do Azure leva em conta esses vários formatos e desempenha automaticamente o melhor link com base nos recursos de plataforma/navegador. Os serviços de mídia também permitem a criptografia dinâmica de ativos com criptografia PlayReady ou criptografia de envelope AES-128 bits. Player de Mídia do Azure permite a descriptografia de conteúdo criptografado do PlayReady e do AES-128, quando configurado adequadamente. 

> [!NOTE]
> A reprodução de HTTPS é necessária para conteúdo criptografado Widevine.

[Inicie sua avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Usar Player de Mídia do Azure página de demonstração

### <a name="start-using"></a>Começar a usar

Você pode usar a [página de demonstração player de mídia do Azure](https://aka.ms/azuremediaplayer) para reproduzir exemplos dos serviços de mídia do Azure ou seu próprio fluxo.  

Para reproduzir um novo vídeo, Cole uma URL diferente e pressione **Atualizar**.

Para configurar várias opções de reprodução (por exemplo, Tech, Language ou Encryption), pressione **Opções avançadas**.

![Leitor de Multimédia do Azure](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorar o diagnóstico de um fluxo de vídeo

Você pode usar a [página de demonstração de player de mídia do Azure](https://aka.ms/azuremediaplayer) para monitorar o diagnóstico de um fluxo de vídeo. 

![Diagnóstico de Player de Mídia do Azure](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Configurar Player de Mídia do Azure em seu HTML

Player de Mídia do Azure é fácil de configurar. Leva apenas alguns minutos para obter a reprodução básica do conteúdo de mídia da sua conta de serviços de mídia. Consulte a [documentação player de mídia do Azure](https://aka.ms/ampdocs) para obter detalhes sobre como configurar e configurar player de mídia do Azure. 

## <a name="next-steps"></a>Passos seguintes

- [Azure Media Player documentation](https://aka.ms/ampdocs) (Documentação do Leitor de Multimédia do Azure)
- [Exemplos de Player de Mídia do Azure](https://aka.ms/ampsamples)

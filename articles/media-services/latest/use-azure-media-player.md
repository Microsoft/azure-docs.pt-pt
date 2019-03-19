---
title: Reprodução com o leitor de multimédia do Azure - Azure | Documentos da Microsoft
description: Este tópico apresenta uma visão geral do leitor de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/03/2018
ms.author: juliako
ms.openlocfilehash: 6de626323c82689d0ead4f5aaad2a2e43187ebd0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896998"
---
# <a name="azure-media-player-overview"></a>Descrição geral de leitor de multimédia do Azure

O leitor de multimédia do Azure é um leitor de vídeo da web criado para reproduzir conteúdo multimédia dos serviços de multimédia do Microsoft Azure numa grande variedade de navegadores e dispositivos. O leitor de multimédia do Azure utiliza padrões da indústria, como HTML5, extensões de origem de mídia (MSE) e Encrypted Media Extensions (EME) para fornecer uma experiência plena de transmissão em fluxo adaptativa. Quando esses padrões não estão disponíveis num dispositivo ou num browser, o leitor de multimédia do Azure utiliza Flash e Silverlight como tecnologia de contingência. Independentemente da tecnologia de reprodução utilizada, os desenvolvedores terão uma interface JavaScript unificada para aceder a APIs. Isso permite a reprodução de conteúdo servido pelos serviços de multimédia do Azure a reprodução numa variedade de toda a de dispositivos e browsers sem qualquer esforço adicional.

Serviços de multimédia do Microsoft Azure permitem que o conteúdo ser atendidas com HLS, DASH, Smooth Streaming formatos de transmissão em fluxo para reproduzir conteúdo de volta. O Azure Media Player leva em conta esses vários formatos e executa automaticamente a ligação melhor, com base nos recursos de plataforma/browser. Serviços de multimédia também permite que a encriptação dinâmica de recursos com encriptação PlayReady ou AES de 128 bits de encriptação de envelope. O leitor de multimédia do Azure permite-lhe PlayReady e AES de 128 bits conteúdo encriptado quando configurado corretamente. 

[Inicie a avaliação gratuita](https://azure.microsoft.com/en-us/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Utilize a página de demonstração do leitor de multimédia do Azure

### <a name="start-using"></a>Começar a utilizar

Pode utilizar o [página de demonstração do leitor de multimédia do Azure](https://aka.ms/amp) para reproduzir exemplos de serviços de multimédia do Azure ou o seu próprio fluxo.  

Para reproduzir um novo vídeo, cole um URL e prima diferentes **atualização**.

Para configurar várias opções de reprodução (por exemplo, tecnológica, idioma ou encriptação), prima **opções avançadas**.

![Media Player do Azure](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitor de diagnóstico de um fluxo de vídeo

Pode utilizar o [página de demonstração do leitor de multimédia do Azure](https://aka.ms/amp) para monitorizar o diagnóstico de um fluxo de vídeo. 

![Diagnóstico do leitor de multimédia do Azure](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Configurar o leitor de multimédia do Azure em seu HTML

O leitor de multimédia do Azure é fácil de configurar. Demora apenas alguns minutos para obter a reprodução básica de conteúdo multimédia da conta dos Media Services. Ver [documentação de leitor de multimédia do Azure](https://aka.ms/ampdocs) para obter detalhes sobre como definir e configurar o leitor de multimédia do Azure. 

## <a name="next-steps"></a>Passos Seguintes

- [Documentação do leitor de multimédia do Azure](https://aka.ms/ampdocs)
- [Exemplos de leitor de multimédia do Azure](https://aka.ms/ampsamples)

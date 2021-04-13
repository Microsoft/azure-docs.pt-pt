---
title: Equipas-quadro da UI Incorporadas
titleSuffix: An Azure Communication Services quickstart
description: Neste documento, você vai aprender como a capacidade de incorporação de equipas-quadro de UI dos Serviços de Comunicação Azure pode ser usada para construir experiências de chamada chave na mão.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8301f81b21db50814df1bc764cc1fae38b4f14de
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307644"
---
# <a name="teams-embed"></a>Equipas Embed

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


Teams Embed é uma capacidade de Serviços de Comunicação Azure focada em interações comuns entre negócios e negócios e chamadas de chamadas. O núcleo do sistema Teams Embed é a [chamada de vídeo e voz](../voice-video-calling/calling-sdk-features.md), mas o sistema Teams Embed baseia-se nos primitivos de chamadas do Azure para oferecer uma experiência completa de utilizador baseada nas reuniões da Microsoft Teams.

As Equipas Embed SDKs são de origem fechada e disponibilizam estas capacidades num formato composto chave-na-mão. Deixa cair equipas incorporadas na tela da sua aplicação e o SDK gera uma experiência completa do utilizador. Como esta experiência de utilizador é muito semelhante às reuniões do Microsoft Teams, pode aproveitar:

- Redução do tempo de desenvolvimento e complexidade da engenharia
- Familiaridade do utilizador final com as equipas
- Capacidade de reutilizar [o conteúdo de treino do utilizador final das equipas](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

O Teams Embed fornece a maioria das funcionalidades suportadas nas reuniões de equipas, incluindo:

- Experiência de pré-reunião onde um utilizador configura os seus dispositivos de áudio e vídeo
- Experiência in-meeting para configurar dispositivos de áudio e vídeo
- [Fundo de vídeo](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): permitir que os participantes desfoquem ou substituam os seus antecedentes
- [Múltiplas opções para a galeria de vídeo](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) grande galeria, modo, foco, pinning e holofotes
- [Partilha de Conteúdos](https://support.microsoft.com/en-us/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): permitir que os participantes partilhem o seu ecrã

Para obter mais informações sobre esta UI em comparação com outros SDKs de comunicação Azure, consulte a introdução do [conceito UI SDK](ui-sdk-overview.md). 

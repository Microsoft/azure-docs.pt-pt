---
title: Diretrizes de privacidade para assistentes de voz no Windows
titleSuffix: Azure Cognitive Services
description: As instruções para ativar a ativação de voz de um agente de voz por defeito.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997515"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Diretrizes de privacidade para assistentes de voz no Windows

É importante que os utilizadores recebam informações claras sobre como os seus dados de voz são recolhidos e utilizados e importantes que lhes seja dado controlo sobre se e como esta coleção acontece. Estas facetas fundamentais da privacidade -- *divulgação* e *consentimento* - são especialmente importantes para assistentes de voz integrados no Windows dada a natureza sempre ouvinte da sua utilização.

Os desenvolvedores que criam assistentes de voz no Windows devem incluir elementos claros de interface de utilizador dentro das suas aplicações que reflitam as capacidades de audição do assistente.

> [!NOTE]
> A não prestação de uma divulgação e consentimento adequados para uma aplicação assistente, incluindo após atualizações da aplicação, pode levar o assistente a ficar indisponível para ativação de voz até que os problemas de privacidade sejam resolvidos. 

## <a name="minimum-requirements-for-feature-inclusion"></a>Requisitos mínimos para a inclusão de recursos

Os utilizadores do Windows podem ver e controlar **`Settings > Privacy > Voice activation`** a disponibilidade das suas aplicações assistentes em .

 > [!div class="mx-imgBorder"]
 > [![listagem de aplicativos de privacidade](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Uma entrada de definição de privacidade de ativação de voz do Windows para uma aplicação assistente")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Para se tornar elegível para inclusão nesta lista, um pedido deve:

1. Em destaque, diga aos seus utilizadores que vai ouvir uma palavra-chave, mesmo quando a aplicação não está a funcionar, e qual é a palavra-chave
1. Inclua uma descrição de como os dados de voz de um utilizador serão utilizados, incluindo um link ou referência a políticas de privacidade relevantes
1. Informe os utilizadores que, além de quaisquer configurações na **`Settings > Privacy > Voice activation`** aplicação, os utilizadores `ms-settings:privacy-voiceactivation` podem visualizar e modificar as suas escolhas de privacidade em, opcionalmente, incluindo um link de protocolo para acesso direto

Depois de cumprir estes requisitos e obter a aprovação da Microsoft, uma aplicação assistente aparecerá na lista de aplicações de ativação de voz uma vez registada nas `Windows.ApplicationModel.ConversationalAgent` APIs e os utilizadores poderão conceder o consentimento à aplicação para ativação de palavras-chave. Por predefinição, ambas `Off` as definições são e exigem que o utilizador visite manualmente a página Definições para ativar.

> [!NOTE]
> Em todos os casos, a permissão de ativação de voz requer permissão do microfone. Se uma aplicação assistente não tiver acesso ao microfone, não será elegível para ativação de voz e aparecerá nas definições de privacidade de ativação de voz num estado desativado.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Requisitos adicionais para a inclusão no consentimento do microfone

Os autores assistentes que pretendam facilitar e suavizar os seus utilizadores podem fazê-lo cumprindo alguns requisitos adicionais para os acima. Depois de os cumprir, a definição padrão de ativação `On` de voz desbloqueada por um dispositivo será depreciável para uma vez (e apenas uma vez) o acesso ao microfone é concedido à aplicação. Isto elimina a necessidade de uma viagem extra às Definições antes de ativar um assistente por voz.

Os requisitos adicionais são que um pedido de assistente deve:

1. **Antes de** pedir o consentimento do `AppCapability.RequestAccessAsync` microfone (por exemplo, utilizando a API), fornecer uma indicação proeminente ao utilizador de que a aplicação assistente gostaria de ouvir a voz de um utilizador para uma palavra-chave, mesmo quando a aplicação não está em execução, e gostaria do consentimento do utilizador
2. Inclua todas as informações **prior** relevantes para a utilização `Windows.ApplicationModel.ConversationalAgent` de dados e políticas de privacidade antes de solicitar o acesso ao microfone ou utilizar as APIs
3. Evite qualquer diretiva ou formulação principal (por exemplo, "clique em sim no seguinte pedido") no fluxo de experiência revelando comportamento de captura de áudio e solicitando permissão

Uma vez preenchidos estes requisitos, um pedido de assistente elegível aparecerá `enabled` na lista de candidaturas elegíveis para ativação de voz num estado assim que o acesso ao microfone for concedido.

> [!NOTE]
> A ativação por voz acima do bloqueio não é elegível para ativação automática com acesso ao microfone e ainda exigirá que um utilizador visite a página de privacidade de ativação de voz para permitir o acesso acima do bloqueio a um assistente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça as melhores práticas para assistentes de voz no Windows](windows-voice-assistants-best-practices.md)
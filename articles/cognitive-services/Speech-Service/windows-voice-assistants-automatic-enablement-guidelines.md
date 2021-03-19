---
title: Diretrizes de privacidade para assistentes de voz no Windows
titleSuffix: Azure Cognitive Services
description: As instruções para ativar a ativação de voz para um agente de voz por defeito.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90987396"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Diretrizes de privacidade para assistentes de voz no Windows

É importante que os utilizadores tenham informações claras sobre como os seus dados de voz são recolhidos e utilizados e importantes para que lhes seja dado controlo sobre se e como esta recolha acontece. Estas facetas fundamentais de privacidade -- *divulgação* e *consentimento* -- são especialmente importantes para assistentes de voz integrados no Windows dada a natureza sempre ouvinte da sua utilização.

Os desenvolvedores que criam assistentes de voz no Windows devem incluir elementos de interface de utilizador claros dentro das suas aplicações que reflitam as capacidades de audição do assistente.

> [!NOTE]
> A não divulgação e consentimento adequados para uma aplicação de assistente, incluindo após atualizações de aplicações, pode resultar na indisponibilidade do assistente para ativação de voz até que os problemas de privacidade sejam resolvidos.

## <a name="minimum-requirements-for-feature-inclusion"></a>Requisitos mínimos para a inclusão de recursos

Os utilizadores do Windows podem ver e controlar a disponibilidade das suas aplicações assistentes em **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![Screenshot mostra opções para controlar a availablidade de Cortana. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Uma entrada de definição de privacidade de ativação por voz do Windows para uma aplicação de assistente")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Para se tornar elegível para inclusão nesta lista, contacte a Microsoft winvoiceassistants@microsoft.com para começar. Por predefinição, os utilizadores terão de ativar explicitamente a ativação de voz para um novo assistente em **`Settings > Privacy > Voice Activation`** , a qual uma aplicação pode ligar-se a `ms-settings:privacy-voiceactivation` . Uma aplicação permitida aparecerá na lista uma vez executada e utilizada as `Windows.ApplicationModel.ConversationalAgent` APIs. As suas definições de ativação de voz serão modificáveis assim que a aplicação tiver obtido o consentimento do microfone do utilizador.

Uma vez que as definições de privacidade do Windows incluem informações sobre como funciona a ativação por voz e que possui uI padrão para controlar a permissão, divulgação e consentimento são cumpridas. O assistente permanecerá nesta lista permitida, desde que não:

* Induzir ou informar mal o utilizador sobre a ativação de voz ou o tratamento de dados de voz pelo assistente
* Interferir indevidamente com outro assistente
* Quebre quaisquer outras políticas relevantes da Microsoft

Se alguma das anteriores forem descobertas, a Microsoft poderá remover um assistente da lista permitida até que os problemas sejam resolvidos.

> [!NOTE]
> Em todos os casos, a permissão de ativação por voz requer permissão para microfone. Se uma aplicação de assistente não tiver acesso ao microfone, não será elegível para ativação de voz e aparecerá nas definições de privacidade de ativação de voz num estado desativado.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Requisitos adicionais para inclusão no consentimento do microfone

Os autores assistentes que pretendam tornar mais fácil e suave para os seus utilizadores optarem pela ativação por voz podem fazê-lo cumprindo requisitos adicionais para cumprir adequadamente a divulgação e o consentimento sem uma viagem extra à página de definições. Uma vez aprovado, a ativação por voz ficará disponível imediatamente assim que um utilizador conceder permissão ao microfone para a aplicação do assistente. Para se qualificar para isso, um pedido de assistente deve fazer o seguinte **antes** de solicitar o consentimento do microfone (por exemplo, utilizando a `AppCapability.RequestAccessAsync` API):

1. Fornecer indicações claras e proeminentes ao utilizador de que a aplicação gostaria de ouvir a voz do utilizador para obter uma palavra-chave, *mesmo quando a aplicação não está em execução*, e gostaria do consentimento do utilizador
1. Incluir informações relevantes sobre a utilização de dados e políticas de privacidade, como um link para uma declaração de privacidade oficial
1. Evite qualquer diretiva ou redação líder (por exemplo, "clique sim no seguinte pedido") no fluxo de experiência que divulga o comportamento de captura de áudio

Se uma aplicação realizar todas as anteriores, é elegível para ativar a capacidade de ativação de voz juntamente com o consentimento do microfone. Contacte winvoiceassistants@microsoft.com para mais informações e para rever uma experiência de primeira utilização.

> [!NOTE]
> A ativação por voz acima do bloqueio não é elegível para ativação automática com acesso ao Microfone e ainda exigirá que um utilizador visite a página de privacidade de ativação de voz para permitir o acesso acima do bloqueio para um assistente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça as melhores práticas para assistentes de voz no Windows](windows-voice-assistants-best-practices.md)
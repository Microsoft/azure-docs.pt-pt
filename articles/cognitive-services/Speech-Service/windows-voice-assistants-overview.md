---
title: Assistentes de voz no Windows - Visão geral
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos assistentes de voz no Windows, incluindo capacidades e recursos de desenvolvimento disponíveis.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: fbcb262fee6a2cc62bfe64e8a8589c92b4fe2b17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82997449"
---
# <a name="voice-assistants-on-windows"></a>Assistentes de voz no Windows

Na versão 10 do Windows 2004 e até 2004, as aplicações de assistente de voz podem aproveitar as APIs do Windows ConversationalAgent para obter uma experiência de assistente completa ativada por voz.

## <a name="voice-assistant-features"></a>Funcionalidades do assistente de voz

As aplicações de agente de voz podem ser ativadas por uma palavra-chave falada para permitir uma experiência mãos-livres e orientada para a voz. A ativação por voz funciona quando a aplicação está fechada e quando o ecrã está bloqueado.

Além disso, o Windows fornece um conjunto de definições de privacidade de ativação por voz que dá aos utilizadores o controlo da ativação por voz e acima da ativação do bloqueio numa base por aplicação.

Após a ativação por voz, o Windows irá gerir corretamente vários agentes ativos e notificará cada assistente de voz se forem interrompidos ou desativados. Isto permite que as aplicações gerem as interrupções e outros eventos inter-agentes corretamente.

## <a name="how-does-voice-activation-work"></a>Como funciona a ativação de voz?

O Tempo de Execução de Ativação do Agente (AAR) é o processo em curso no Windows que gere a ativação da aplicação numa palavra-chave falada ou num botão. Começa com o Windows desde que exista pelo menos uma aplicação no sistema que esteja registada no sistema. As aplicações interagem com a AAR através das APIs do ConversationalAgent no Windows SDK.

Quando o utilizador fala uma palavra-chave, o spotter de palavras-chave de software ou hardware no sistema notifica a AAR de que foi detetada uma palavra-chave, fornecendo um ID de palavra-chave. A AAR, por sua vez, envia um pedido ao BackgroundService para iniciar a aplicação com o iD da aplicação correspondente.

### <a name="registration"></a>Registo

A primeira vez que uma aplicação ativada por voz é executada, regista o seu ID de aplicação e informações de palavras-chave através das APIs do ConversationalAgent. A AAR regista todas as configurações no mapeamento global com o spotter de palavras-chave de hardware ou software no sistema, permitindo-lhes detetar a palavra-chave da aplicação. A aplicação também [se regista com o Serviço de Fundo.](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)

Note que isto significa que uma aplicação não pode ser ativada por voz até que tenha sido executada uma vez e o registo tenha sido autorizado a ser concluído.

### <a name="receiving-an-activation"></a>Receber uma ativação

Ao receber o pedido da AAR, o Serviço de Fundo lança a aplicação. A aplicação recebe um sinal através do método do ciclo de vida onBackgroundActivated com um argumento único do `App.xaml.cs` evento. Este argumento diz à aplicação que foi ativada pela AAR e que deve iniciar a verificação de palavras-chave.

Se a aplicação verificar com sucesso a palavra-chave, pode fazer um pedido para ser mostrado em primeiro plano. Quando este pedido é bem sucedido, a aplicação apresenta UI e continua a sua interação com o utilizador.

A AAR ainda assinala aplicações ativas quando a sua palavra-chave é falada. Em vez de assinalar através do método do ciclo de vida em `App.xaml.cs` , no entanto, sinaliza através de um evento nas APIs do ConversationalAgent.

### <a name="keyword-verification"></a>Verificação de palavras-chave

O spotter de palavras-chave que desencadeia a aplicação para iniciar conseguiu um baixo consumo de energia simplificando o modelo de palavras-chave. Isto permite que o spotter de palavras-chave esteja "sempre ligado" sem um impacto de alta potência, mas também significa que o spotter de palavras-chave provavelmente terá um elevado número de "aceitações falsas" onde deteta uma palavra-chave, mesmo que nenhuma palavra-chave tenha sido falada. É por isso que o sistema de ativação por voz lança a aplicação em segundo plano: para dar à aplicação a oportunidade de verificar se a palavra-chave foi falada antes de interromper a sessão atual do utilizador. A AAR guarda o áudio alguns segundos antes da palavra-chave ser detetada e torna-o acessível à aplicação. A aplicação pode usar isto para executar um spotter de palavras-chave mais fiável no mesmo áudio.

## <a name="next-steps"></a>Passos seguintes

- **Reveja as diretrizes de design:** As [nossas diretrizes de design](windows-voice-assistants-best-practices.md) estabelecem o trabalho de chave necessário para fornecer as melhores experiências possíveis para a ativação de voz no Windows 10.
- **Visite a página 'Começar':** Comece [aqui](how-to-windows-voice-assistants-get-started.md) para que os passos comecem a implementar assistentes de voz no Windows, desde definir o seu ambiente de desenvolvimento através de uma introdução ao guia de implementação.
- **Experimente a aplicação da amostra**: Para experimentar estas capacidades em primeira mão, visite a página [UWP Voice Assistant Sample](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) e siga os passos para pôr o cliente da amostra a funcionar.

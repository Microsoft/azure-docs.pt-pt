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
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997449"
---
# <a name="voice-assistants-on-windows"></a>Assistentes de voz no Windows

Na versão 2004 e up do Windows 10, as aplicações de assistente de voz podem tirar partido das APIs do Agente de Conversação do Windows para obter uma experiência de assistente completa ativada por voz.

## <a name="voice-assistant-features"></a>Características do assistente de voz

As aplicações do agente de voz podem ser ativadas por uma palavra-chave falada para permitir uma experiência ativada por voz. A ativação de voz funciona quando a aplicação está fechada e quando o ecrã está bloqueado.

Além disso, o Windows fornece um conjunto de definições de privacidade de ativação de voz que proporcionam aos utilizadores o controlo da ativação de voz e a ativação acima do bloqueio numa base por app.

Após a ativação por voz, o Windows irá gerir corretamente vários agentes ativos e notificar cada assistente de voz se forem interrompidos ou desativados. Isto permite que as aplicações gerem adequadamente interrupções e outros eventos inter-agentes.

## <a name="how-does-voice-activation-work"></a>Como funciona a ativação da voz?

O Tempo de Execução de Ativação do Agente (AAR) é o processo em curso no Windows que gere a ativação da aplicação numa palavra-chave ou pressão de botão falado. Começa com o Windows desde que haja pelo menos uma aplicação no sistema que esteja registada no sistema. As aplicações interagem com a AAR através das APIs do ConversationalAgent no Windows SDK.

Quando o utilizador fala uma palavra-chave, o spotter de palavras-chave de software ou hardware no sistema notifica a AAR de que foi detetada uma palavra-chave, fornecendo um ID de palavra-chave. A ArA, por sua vez, envia um pedido ao BackgroundService para iniciar a aplicação com o ID de aplicação correspondente.

### <a name="registration"></a>Registo

A primeira vez que uma aplicação ativada por voz é executada, regista a sua informação de ID da aplicação e palavra-chave através das APIs do Agente de Conversação. A AAR regista todas as configurações no mapeamento global com o observador de palavras-chave de hardware ou software no sistema, permitindo-lhes detetar a palavra-chave da aplicação. A aplicação também [se regista no Serviço de Fundo.](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)

Note que isto significa que uma aplicação não pode ser ativada por voz até que tenha sido executada uma vez e o registo tenha sido autorizado a ser concluído.

### <a name="receiving-an-activation"></a>Receber uma ativação

Ao receber o pedido da AAR, o Serviço de Fundo lança a aplicação. A aplicação recebe um sinal através do método `App.xaml.cs` de ciclo de vida OnBackgroundActivated com um argumento único de evento. Este argumento diz à aplicação que foi ativada pela ArA e que deve iniciar a verificação das palavras-chave.

Se a aplicação verificar com sucesso a palavra-chave, pode fazer um pedido a mostrar em primeiro plano. Quando este pedido é bem sucedido, a aplicação exibe UI e continua a sua interação com o utilizador.

A ARa ainda sinaliza aplicações ativas quando a sua palavra-chave é falada. Em vez de sinalizar através do `App.xaml.cs`método do ciclo de vida em, no entanto, sinaliza através de um evento nas APIs do Agente conversador.

### <a name="keyword-verification"></a>Verificação de palavras-chave

O spotter de palavra-chave que desencadeia a aplicação para iniciar alcançou um baixo consumo de energia simplificando o modelo de palavra-chave. Isto permite que o observador de palavras-chave esteja "sempre ligado" sem um impacto de alta potência, mas também significa que o observador de palavras-chave provavelmente terá um elevado número de "falsas aceitações" onde deteta uma palavra-chave, mesmo que nenhuma palavra-chave tenha sido dita. É por isso que o sistema de ativação por voz lança a aplicação em segundo plano: para dar à aplicação a oportunidade de verificar se a palavra-chave foi falada antes de interromper a sessão atual do utilizador. A ArA guarda o áudio uma vez que alguns segundos antes da palavra-chave ser detetada e torna-o acessível à aplicação. A aplicação pode usá-lo para executar um observador de palavras-chave mais fiável no mesmo áudio.

## <a name="next-steps"></a>Passos seguintes

- **Reveja as diretrizes de conceção:** As [nossas diretrizes](windows-voice-assistants-best-practices.md) de design estabelecem o trabalho-chave necessário para proporcionar as melhores experiências possíveis para a ativação de voz no Windows 10.
- **Visite a página Getting Started:** Comece [aqui](how-to-windows-voice-assistants-get-started.md) para que os passos comecem a implementar assistentes de voz no Windows, desde a definição do seu ambiente de desenvolvimento através de uma introdução ao guia de implementação.
- **Experimente a aplicação de amostras**: Para experimentar estas capacidades em primeira mão, visite a página de Amostra de Assistente de [Voz da UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) e siga os passos para que o cliente da amostra execute.

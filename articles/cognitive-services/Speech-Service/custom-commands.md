---
title: Comandos personalizados - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral das funcionalidades, capacidades e restrições para Comandos Personalizados, uma solução para criar aplicações de voz.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: c9f7168bc0524b483413ade9792af18ff9cfebfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358547"
---
# <a name="what-is-custom-commands"></a>O que são Comandos Personalizados?

Aplicações como [assistentes de voz](voice-assistants.md) ouvem os utilizadores e tomam medidas em resposta, muitas vezes respondendo. Usam [a palavra-a-texto para](speech-to-text.md) transcrever o discurso do utilizador e, em seguida, tomam medidas sobre a compreensão da linguagem natural do texto. Esta ação inclui frequentemente a saída falada do assistente gerado com [texto-a-voz](text-to-speech.md). Os dispositivos ligam-se a assistentes com o objeto do SDK do `DialogServiceConnector` discurso.

**Os Comandos Personalizados** facilitam a construção de aplicações de comando de voz ricas otimizadas para experiências de interação de voz. Proporciona uma experiência de autoria unificada, um modelo de hospedagem automática e uma complexidade relativamente menor, ajudando-o a concentrar-se na construção da melhor solução para os seus cenários de comando de voz.

Os Comandos Personalizados são mais adequados para a conclusão de tarefas ou cenários de comando e controlo, particularmente bem combinados para dispositivos de Internet of Things (IoT), dispositivos ambiente e sem cabeça. Exemplos incluem soluções para indústrias de Hotelaria, Retalho e Automóvel, permitindo-lhe construir as melhores experiências controladas por voz no quarto para os seus hóspedes, gerir o inventário na sua loja e controlar a funcionalidade no carro enquanto está em movimento.

> [!TIP]
> Veja as nossas demonstrações de amostra na nossa página de aterragem [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) em.

Se estiver interessado em construir aplicações de conversação complexas, é encorajado a experimentar a Estrutura bot usando a [Solução Assistente Virtual.](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) Pode adicionar voz a qualquer bot de estrutura de bot usando Direct Line Speech.

Os bons candidatos a Comandos Personalizados têm um vocabulário fixo com conjuntos bem definidos de variáveis. Por exemplo, as tarefas de domótica, como controlar um termóstato, são ideais.

   ![Exemplos de cenários de conclusão de tarefas](media/voice-assistants/task-completion-examples.png "exemplos de conclusão de tarefas")

## <a name="getting-started-with-custom-commands"></a>Começar com comandos personalizados

O nosso objetivo com comandos personalizados é reduzir a sua carga cognitiva para aprender todas as diferentes tecnologias e focar a construção da sua app de comando de voz. Primeiro passo para a utilização de Comandos Personalizados para <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">criar um recurso <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Speech </a>. Pode ser autor da sua aplicação Comandos Personalizados no Estúdio de Discurso e publicá-la, após a qual uma aplicação no dispositivo pode comunicar com ela utilizando o Speech SDK.

#### <a name="authoring-flow-for-custom-commands"></a>Fluxo de autoria para comandos personalizados
   ![Fluxo de autoria para comandos personalizados](media/voice-assistants/custom-commands-flow.png "O fluxo de autoria de Comandos Personalizados")

Siga o nosso quickstart para ter o seu primeiro código de execução de comandos personalizados em menos de 10 minutos.

* [Criar um assistente de voz com os Comandos Personalizados](quickstart-custom-commands-application.md)

Assim que terminar o quickstart, explore os nossos guias de como fazer para obter passos detalhados para conceber, desenvolver, depurar, implementar e integrar uma aplicação de Comandos Personalizados.

## <a name="building-voice-assistants-with-custom-commands"></a>Construindo assistentes de voz com comandos personalizados
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Passos seguintes

* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
* [Veja os nossos Assistentes de Voz repo no GitHub para amostras](https://aka.ms/speech/cc-samples)
* [Vá ao Estúdio de Fala para experimentar comandos personalizados](https://speech.microsoft.com/customcommands)
* [Obtenha o Discurso SDK](speech-sdk.md)

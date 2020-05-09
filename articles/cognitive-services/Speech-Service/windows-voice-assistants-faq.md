---
title: Assistentes de Voz no Windows - FAQ
titleSuffix: Azure Cognitive Services
description: Perguntas comuns que surgem frequentemente durante o desenvolvimento do agente de voz Windows.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: aa71057d3819acb335153ee5b4b65960320405be
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997416"
---
# <a name="samples-and-faqs"></a>Amostras e Perguntas Frequentes

## <a name="the-uwp-voice-assistant-sample"></a>A amostra de assistente de voz da UWP

A Amostra de Assistente de Voz do UWP é um assistente de voz no Windows que serve para

- demonstrar o uso das APIs do Agente de Conversação do Windows
- exibir as melhores práticas para um agente de voz no Windows
- fornecer uma aplicação adaptável e componentes reutilizáveis para a sua aplicação de agente MVP
- mostrar como o Discurso de Linha Direta, juntamente com o Quadro Bot ou comandos personalizados, pode ser usado juntamente com as APIs do Agente de Conversação do Windows para uma experiência de agente de voz de ponta a ponta

A documentação fornecida com a aplicação da amostra percorre o código de ativação de voz e gestão de agentes, desde os pré-requisitos do arranque através de uma limpeza adequada.

> [!div class="nextstepaction"]
> [Visite o repo github para a amostra da UWP](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Como posso contactar a Microsoft para obter recursos como fichas de acesso limitado e ficheiros de modelos de palavras-chave?

Contacto winvoiceassistants@microsoft.com para solicitar estes recursos.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>A minha aplicação está a aparecer numa pequena janela quando a ativo por voz. Como posso transitar da vista compacta para uma janela de aplicação completa?

Quando a sua aplicação é ativada pela primeira vez por voz, é iniciada numa vista compacta. Leia a [orientação do Design para visualização](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) de ativação de voz para orientação sobre as diferentes visões e transições entre elas para assistentes de voz no Windows.

Para fazer a transição da vista compacta para `TryEnterViewModeAsync`a visão completa da aplicação, utilize a appView API:

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Porque é que as funcionalidades do assistente de voz no Windows só estão ativadas para aplicações UWP?

Tendo em conta os riscos de privacidade associados a funcionalidades como a ativação de voz, as funcionalidades da plataforma UWP são necessárias para permitir que as funcionalidades do assistente de voz no Windows sejam suficientemente seguras.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>A amostra de assistente de voz da UWP usa o Discurso da Linha Direta. Tenho de usar o Discurso da Linha Direta para o meu assistente de voz no Windows?

A Aplicação de Amostras UWP foi desenvolvida usando o Direct Line Speech e o Speech Services SDK como uma demonstração de como usar um serviço de diálogo com a capacidade do Agente Conversacional windows. No entanto, pode utilizar qualquer serviço para verificação de palavras-chave locais e em nuvem, conversão de discurso-texto, diálogo bot e conversão de texto-a-fala.
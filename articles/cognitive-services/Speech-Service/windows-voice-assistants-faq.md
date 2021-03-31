---
title: Assistentes de voz no Windows - FAQ
titleSuffix: Azure Cognitive Services
description: Perguntas comuns que surgem frequentemente durante o desenvolvimento do agente de voz do Windows.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 3cb4dd16592f3af962b32fe3005d2ad4b241d831
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "84457019"
---
# <a name="samples-and-faqs"></a>Amostras e FaQs

## <a name="the-uwp-voice-assistant-sample"></a>A amostra do assistente de voz uWP

A Amostra de Assistente de Voz UWP é um assistente de voz no Windows que serve para

- demonstrar a utilização das APIs do Windows ConversationalAgent
- exibir as melhores práticas para um agente de voz no Windows
- fornecer uma aplicação adaptável e componentes reutilizáveis para a sua aplicação de agente MVP
- mostrar como o Direct Line Speech, juntamente com o Bot Framework ou comandos personalizados, podem ser usados juntamente com as APIs do Windows ConversationalAgent para uma experiência de agente de voz de ponta a ponta

A documentação fornecida com a app da amostra percorre o caminho de código da ativação de voz e gestão de agentes, desde os pré-requisitos do arranque até à limpeza adequada.

> [!div class="nextstepaction"]
> [Visite o gitHub repo para a amostra do UWP](https://aka.ms/MVA/sample)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-contact-microsoft-for-resources-like-limited-access-feature-tokens-and-keyword-model-files"></a>Como posso contactar a Microsoft por recursos como fichas de funcionalidade de acesso limitada e ficheiros de modelos de palavras-chave?

Contacto winvoiceassistants@microsoft.com para solicitar estes recursos.

### <a name="my-app-is-showing-in-a-small-window-when-i-activate-it-by-voice-how-can-i-transition-from-the-compact-view-to-a-full-application-window"></a>A minha aplicação está a aparecer numa pequena janela quando a ativo por voz. Como posso transitar da vista compacta para uma janela de aplicação completa?

Quando a sua aplicação é ativada pela primeira vez por voz, é iniciada numa vista compacta. Leia a [orientação do Design para pré-visualização de ativação de voz](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) para obter orientações sobre as diferentes visões e transições entre eles para assistentes de voz no Windows.

Para fazer a transição de vista compacta para vista completa da aplicação, utilize a appView API `TryEnterViewModeAsync` :

`var appView = ApplicationView.GetForCurrentView();
 await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);`

### <a name="why-are-voice-assistant-features-on-windows-only-enabled-for-uwp-applications"></a>Porque é que as funcionalidades de assistente de voz no Windows só estão ativadas para aplicações UWP?

Tendo em conta os riscos de privacidade associados a funcionalidades como a ativação por voz, as funcionalidades da plataforma UWP são necessárias para permitir que as funcionalidades do assistente de voz no Windows sejam suficientemente seguras.

### <a name="the-uwp-voice-assistant-sample-uses-direct-line-speech-do-i-have-to-use-direct-line-speech-for-my-voice-assistant-on-windows"></a>A amostra de assistente de voz UWP utiliza discurso de linha direta. Tenho de usar o Discurso da Linha Direta para o meu assistente de voz no Windows?

A Aplicação de Amostra uWP foi desenvolvida usando o Direct Line Speech e o Speech Services SDK como uma demonstração de como usar um serviço de diálogo com a capacidade do Windows Conversational Agent. No entanto, pode utilizar qualquer serviço para verificação de palavras-chave locais e em nuvem, conversão de palavras-chave fala-a-texto, diálogo de bot e conversão texto-a-voz.
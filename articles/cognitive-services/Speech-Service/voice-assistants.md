---
title: Assistentes de voz - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral das funcionalidades, capacidades e restrições para assistentes de voz usando o Kit de Desenvolvimento de Software de Fala (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369901"
---
# <a name="what-is-a-voice-assistant"></a>O que é um assistente de voz?

Assistentes de voz que usam o serviço Speech capacitam os desenvolvedores a criar interfaces de conversação naturais e humanas para as suas aplicações e experiências.

O serviço de assistente de voz proporciona uma interação rápida e fiável entre um dispositivo e uma implementação de assistente que utiliza (1) o canal de discurso de linha direta do Bot Framework ou (2) o serviço integrado de Comandos Personalizados (Pré-visualização) para a conclusão da tarefa.

As aplicações ligam-se ao serviço de assistente de voz com o Kit de Desenvolvimento de Software de Fala (SDK).

   ![Diagrama conceptual do fluxo de serviço de orquestração assistente de voz](media/voice-assistants/overview.png "O fluxo de assistente de voz")

## <a name="choosing-an-assistant-solution"></a>Escolher uma solução de assistente

O primeiro passo para criar um assistente de voz é decidir o que deve fazer. O serviço De Fala fornece múltiplas soluções complementares para criar as suas interações assistentes. Quer queira a flexibilidade e versatilidade que o canal de discurso de [linha direta](direct-line-speech.md) do Bot Framework proporciona ou a simplicidade dos [Comandos Personalizados (Pré-visualização)](custom-commands.md) para cenários simples, a seleção das ferramentas certas irá começar.

| Se quiseres... | Então considere... | Por exemplo... |
|-------------------|------------------|----------------|
|Conversa aberta com integração robusta de competências e controlo total de implementação | O canal de discurso de [linha direta](direct-line-speech.md) do Quadro Bot | <ul><li>"Preciso de ir a Seattle"</li><li>"Que tipo de pizza posso pedir?"</li></ul>
|Comando e controlo ou conversa orientada para tarefas com autore hospedagem simplificada | [Comandos Personalizados (Pré-visualização)](custom-commands.md) | <ul><li>"Ligue a luz aérea"</li><li>"Torná-lo 5 graus mais quente"</ul>

Recomendamos o [Direct Line Speech](direct-line-speech.md) como a melhor escolha por defeito se ainda não tiver a certeza do que gostaria que o seu assistente manuseasse. Oferece integração com um conjunto rico de ferramentas e ajudas de autoria, como a [Solução Assistente Virtual e o Modelo de Empresa](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) e o serviço [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) para construir padrões comuns e usar as suas fontes de conhecimento existentes.

[Os Comandos Personalizados (Pré-visualização)](custom-commands.md) proporcionam uma experiência de autoria e hospedagem simplificada especificamente adaptada para cenários de comando e controlo de linguagem natural.

   ![Comparação de soluções assistentes](media/voice-assistants/assistant-solution-comparison.png "Comparação de soluções assistentes")

## <a name="core-features"></a>Características do núcleo

Quer escolha Direct [Line Speech](direct-line-speech.md) ou [Custom Commands (Preview)](custom-commands.md) para criar interações do seu assistente, pode utilizar um conjunto rico de funcionalidades de personalização para personalizar o seu assistente à sua marca, produto e personalidade.

| Categoria | Funcionalidades |
|----------|----------|
|[Palavra-chave personalizada](speech-devices-sdk-create-kws.md) | Os utilizadores podem iniciar conversas com assistentes com uma palavra-chave personalizada como "Hey Contoso". Uma aplicação faz isso com um motor de palavra-chave personalizado no Speech SDK, que pode ser configurado com uma palavra-chave personalizada [que você pode gerar aqui](speech-devices-sdk-create-kws.md). Os assistentes de voz podem utilizar a verificação das palavras-chave do lado do serviço para melhorar a precisão da ativação da palavra-chave (contra o dispositivo apenas).
|[Discurso ao texto](speech-to-text.md) | Assistentes de voz convertem áudio em tempo real em texto reconhecido usando [o Discurso-a-texto](speech-to-text.md) do serviço de Fala. Este texto está disponível, tal como é transcrito, tanto para a sua implementação como para a sua aplicação ao cliente.
|[Texto para a fala](text-to-speech.md) | As respostas texuais do seu assistente são sintetizadas usando [o texto-a-fala](text-to-speech.md) do serviço de Fala. Esta síntese é então disponibilizada à sua aplicação cliente como um fluxo de áudio. A Microsoft oferece a capacidade de construir a sua própria voz Neural TTS personalizada e de alta qualidade que dá voz à sua marca. Para saber mais, [contacte-nos.](mailto:mstts@microsoft.com)

## <a name="getting-started-with-voice-assistants"></a>Começar com assistentes de voz

Oferecemos quickstarts projetados para tê-lo em funcionamento código em menos de 10 minutos. Esta tabela inclui uma lista de quickstarts assistentes de voz, organizados pela linguagem.

| Início Rápido | Plataforma | Referência da API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| Java | Janelas, macOS, Linux | [Procurar](https://aka.ms/csspeech/javaref) |
| Java | Android | [Procurar](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de amostra para a criação de um assistente de voz está disponível no GitHub. Estas amostras cobrem o pedido do cliente para a ligação ao seu assistente em várias línguas de programação populares.

* [Amostras de assistente de voz (SDK)](https://aka.ms/csspeech/samples)
* [Tutorial: Voz habilita o seu assistente com o SDK de Fala, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Tutorial

Um tutorial sobre como [ativar o seu assistente usando o canal Speech SDK e direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personalização

Os assistentes de voz construídos com o serviço Speech podem usar toda a gama de opções de personalização disponíveis para [o discurso-a-texto,](speech-to-text.md) [texto-a-fala](text-to-speech.md)e [seleção de palavras-chave personalizadas.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> As opções de personalização variam de forma linguística/local (ver [idiomas suportados).](supported-languages.md)

## <a name="reference-docs"></a>Doutorados de referência

* [API de Voz](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
* [Obtenha o SDK do Discurso](speech-sdk.md)
* [Saiba mais sobre comandos personalizados (Pré-visualização)](custom-commands.md)
* [Saiba mais sobre discurso de linha direta](direct-line-speech.md)

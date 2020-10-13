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
ms.openlocfilehash: 4bdca8921a99db764cbc8ee1edfdad8cf8b8568f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360944"
---
# <a name="what-is-a-voice-assistant"></a>O que é um assistente de voz?

Os assistentes de voz que usam o serviço Speech capacitam os desenvolvedores a criar interfaces de conversação naturais e humanas para as suas aplicações e experiências.

O serviço de assistente de voz proporciona uma interação rápida e fiável entre um dispositivo e uma implementação de assistente que utiliza (1) [Direct Line Speech](direct-line-speech.md) (via Azure Bot Service) para adicionar capacidades de voz aos seus bots, ou, (2) Comandos Personalizados para cenários de comando de voz.

## <a name="choosing-an-assistant-solution"></a>Escolher uma solução de assistente

O primeiro passo para criar um assistente de voz é decidir o que deve fazer. O serviço Discurso fornece soluções múltiplas e complementares para criar as suas interações de assistente. Pode adicionar capacidades de voz e voz ao seu bot flexível e versátil construído utilizando o Azure Bot Service com o canal [Direct Line Speech,](direct-line-speech.md) ou aproveitar a simplicidade de autoria de uma aplicação [de Comandos Personalizados](custom-commands.md) para cenários simples de comando de voz.

| Se quiseres... | Então considere... | Por exemplo... |
|-------------------|------------------|----------------|
|Conversa aberta com integração robusta de competências e controlo de implementação total | Bot de serviço Azure Bot com canal [de fala de linha direta](direct-line-speech.md) | <ul><li>"Preciso de ir a Seattle"</li><li>"Que tipo de pizza posso pedir?"</li></ul>
|Comando de voz ou simples conversas orientadas para tarefas com autoria e hospedagem simplificadas | [Comandos Personalizados](custom-commands.md) | <ul><li>"Acenda a luz aérea"</li><li>"Torná-lo 5 graus mais quente"</li><li>Outras amostras [disponíveis aqui](https://speech.microsoft.com/customcommands)</li></ul>

Recomendamos o [Direct Line Speech](direct-line-speech.md) como a melhor escolha padrão se ainda não tiver a certeza do que gostaria que o seu assistente manuseasse. Oferece integração com um rico conjunto de ferramentas e ajudas de autoria, como a [Solução assistente virtual e o modelo de empresa](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) e o serviço [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) para construir padrões comuns e usar as suas fontes de conhecimento existentes.

[Os Comandos Personalizados](custom-commands.md) facilitam a construção de aplicações de comando de voz ricas otimizadas para experiências de interação de voz. Proporciona uma experiência de autoria unificada, um modelo de hospedagem automática e uma complexidade relativamente menor, ajudando-o a concentrar-se na construção da melhor solução para os seus cenários de comando de voz.

   ![Comparação de soluções de assistente](media/voice-assistants/assistant-solution-comparison.png "Comparação de soluções de assistente")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Arquitetura de referência para a construção de um assistente de voz usando o Speech SDK

   ![Diagrama conceptual do fluxo de serviço de orquestração assistente de voz](media/voice-assistants/overview.png "O fluxo de assistente de voz")

## <a name="core-features"></a>Características do núcleo

Quer escolha [Direct Line Speech](direct-line-speech.md) ou [Comandos Personalizados](custom-commands.md) para criar as interações do seu assistente, pode utilizar um rico conjunto de funcionalidades de personalização para personalizar o seu assistente à sua marca, produto e personalidade.

| Categoria | Funcionalidades |
|----------|----------|
|[Palavra-chave personalizada](speech-devices-sdk-create-kws.md) | Os utilizadores podem iniciar conversas com assistentes com uma palavra-chave personalizada como "Hey Contoso". Uma aplicação faz isso com um motor de palavra-chave personalizado no Speech SDK, que pode ser configurado com uma palavra-chave personalizada [que pode gerar aqui.](speech-devices-sdk-create-kws.md) Os assistentes de voz podem utilizar a verificação de palavras-chave do lado do serviço para melhorar a precisão da ativação da palavra-chave (contra o dispositivo sozinho).
|[Discurso ao texto](speech-to-text.md) | Os assistentes de voz convertem o áudio em tempo real em texto reconhecido utilizando [o speech-to-text](speech-to-text.md) do serviço Speech. Este texto está disponível, tal como é transcrito, tanto para a implementação do seu assistente como para a sua aplicação ao cliente.
|[Texto para a fala](text-to-speech.md) | As respostas texuais do seu assistente são sintetizadas usando [texto-a-discurso](text-to-speech.md) do serviço Discurso. Esta síntese é então disponibilizada à aplicação do seu cliente como um fluxo de áudio. A Microsoft oferece a capacidade de construir a sua própria voz neural TTS personalizada e de alta qualidade que dá voz à sua marca. Para saber mais, [contacte-nos.](mailto:mstts@microsoft.com)

## <a name="getting-started-with-voice-assistants"></a>Começar com assistentes de voz

Oferecemos quickstarts projetados para que você execute código em menos de 10 minutos. Esta tabela inclui uma lista de quickstarts assistentes de voz, organizadas pela linguagem.

* [Quickstart: Criar um assistente de voz personalizado usando o Discurso da Linha Direta](quickstarts/voice-assistants.md)
* [Quickstart: Construa uma aplicação de comando de voz usando Comandos Personalizados](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Código de amostra e tutoriais

O código de amostra para criar um assistente de voz está disponível no GitHub. Estas amostras cobrem a aplicação do cliente para ligação ao seu assistente em várias línguas de programação populares.

* [Amostras de assistente de voz no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Tutorial: Ativar a voz do seu assistente construído utilizando o Serviço Azure Bot com o SDK de discurso C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Tutorial: Criar uma aplicação de comandos personalizados com comandos de voz simples](how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="customization"></a>Personalização

Os assistentes de voz construídos usando os serviços de Discurso Azure podem utilizar toda a gama de opções de personalização.

* [Discurso personalizado](how-to-custom-speech.md)
* [Voz Personalizada](how-to-custom-voice.md)
* [Palavra-chave personalizada](custom-keyword-overview.md)

> [!NOTE]
> As opções de personalização variam de acordo com o idioma/local (ver [idiomas suportados).](language-support.md)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
* [Saiba mais sobre Comandos Personalizados](custom-commands.md)
* [Saiba mais sobre o Discurso da Linha Direta](direct-line-speech.md)
* [Obtenha o Discurso SDK](speech-sdk.md)
---
title: Discurso de Linha Direta - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral das funcionalidades, capacidades e restrições para assistentes de voz usando o Direct Line Speech com o Kit de Desenvolvimento de Software de Fala (SDK).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: e8c7e21b0784aacc85bd02e3c1702e1a710e76ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021886"
---
# <a name="what-is-direct-line-speech"></a>O que é a Voz do Direct Line?

**O Direct Line Speech** é uma solução robusta e de ponta a ponta para criar um assistente de voz flexível e extensível. É alimentado pelo Bot Framework e pelo seu canal Direct Line Speech, que é otimizado para a interação voice-in com bots.

[Os assistentes de](voice-assistants.md) voz ouvem os utilizadores e tomam medidas em resposta, muitas vezes respondendo. Usam [a palavra-a-texto para](speech-to-text.md) transcrever o discurso do utilizador e, em seguida, tomam medidas sobre a compreensão da linguagem natural do texto. Esta ação inclui frequentemente a saída falada do assistente gerado com [texto-a-voz](text-to-speech.md).

O Direct Line Speech oferece os mais altos níveis de personalização e sofisticação para assistentes de voz. É projetado para cenários de conversação que são abertos, naturais ou híbridos dos dois com conclusão de tarefa ou uso de comando e controlo. Este alto grau de flexibilidade vem com uma maior complexidade, e cenários que são alargados a tarefas bem definidas usando a entrada de linguagem natural podem querer considerar [comandos personalizados (Preview)](custom-commands.md) para uma experiência de solução simplificada.

## <a name="getting-started-with-direct-line-speech"></a>Começando com discurso de linha direta

Os primeiros passos para criar um assistente de voz usando o Direct Line Speech são [obter uma chave de subscrição de discursos](overview.md#try-the-speech-service-for-free), criar um novo bot associado a essa subscrição e ligar o bot ao canal Direct Line Speech.

   ![Diagrama conceptual do fluxo de serviço de orquestração do discurso da linha direta](media/voice-assistants/overview-directlinespeech.png "O fluxo do Canal da Fala")

Para obter um guia completo e passo a passo sobre a criação de um simples assistente de voz utilizando o Direct Line Speech, consulte [o tutorial para permitir a fala do seu bot com o Speech SDK e o canal Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

Também oferecemos quickstarts projetados para que você executa o código e aprendendo as APIs rapidamente. Esta tabela inclui uma lista de quickstarts assistentes de voz organizados por linguagem e plataforma.

| Início Rápido | Plataforma | Referência da API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procurar](/dotnet/api/microsoft.cognitiveservices.speech) |
| Java | Windows, macOS, Linux | [Procurar](/java/api/com.microsoft.cognitiveservices.speech) |
| Java | Android | [Procurar](/java/api/com.microsoft.cognitiveservices.speech) |

## <a name="sample-code"></a>Código de exemplo

O código de amostra para criar um assistente de voz está disponível no GitHub. Estas amostras cobrem a aplicação do cliente para ligação ao seu assistente em várias línguas de programação populares.

* [Amostras de assistente de voz (SDK)](https://aka.ms/csspeech/samples)
* [Tutorial: A voz permite o seu assistente com o Discurso SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personalização

Os assistentes de voz construídos através do serviço speech podem utilizar toda a gama de opções de personalização disponíveis para [discurso-a-texto,](speech-to-text.md) [texto-a-voz](text-to-speech.md)e [seleção de palavras-chave personalizadas](./custom-keyword-basics.md).

> [!NOTE]
> As opções de personalização variam de acordo com o idioma/local (ver [idiomas suportados).](./language-support.md)

O Discurso de Linha Direta e a sua funcionalidade associada para assistentes de voz são um suplemento ideal para a [Solução de Assistente Virtual e Modelo de Empresa.](/azure/bot-service/bot-builder-enterprise-template-overview) Embora o Direct Line Speech possa trabalhar com qualquer bot compatível, estes recursos fornecem uma linha de base reutilizável para experiências de conversação de alta qualidade, bem como habilidades e modelos de suporte comuns para começar rapidamente.

## <a name="reference-docs"></a>Documentos de referência

* [API de Voz](./speech-sdk.md)
* [Azure Bot Service](/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Próximos passos

* [Obtenha gratuitamente uma chave de subscrição de serviço de discurso](overview.md#try-the-speech-service-for-free)
* [Obtenha o Discurso SDK](speech-sdk.md)
* [Criar e implementar um bot básico](/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obtenha a solução de assistente virtual e o modelo de empresa](https://github.com/Microsoft/AI)
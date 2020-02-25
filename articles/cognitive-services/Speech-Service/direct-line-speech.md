---
title: Discurso de Linha Direta - Serviço de Discurso
titleSuffix: Azure Cognitive Services
description: Uma visão geral das funcionalidades, capacidades e restrições para assistentes de voz usando o Direct Line Speech com o Kit de Desenvolvimento de Software de Fala (SDK).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 230fbd14ce33b52b1e7a1f9cc9cd530ccdec169a
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562535"
---
# <a name="about-direct-line-speech"></a>Sobre o Discurso da Linha Direta

**O Discurso de Linha Direta** é uma solução robusta e de ponta a ponta para criar um assistente de voz flexível e extensível. É alimentado pelo Bot Framework e pelo seu canal Direct Line Speech, que está otimizado para a interação de voz e de voz com bots.

[Os assistentes](voice-assistants.md) de voz ouvem os utilizadores e tomam uma ação em resposta, muitas vezes a responder. Usam [o discurso ao texto](speech-to-text.md) para transcrever o discurso do utilizador e, em seguida, tomam medidas sobre a compreensão da linguagem natural do texto. Esta ação inclui frequentemente a saída falada do assistente gerado com [texto-a-fala](text-to-speech.md).

O Discurso de Linha Direta oferece os mais altos níveis de personalização e sofisticação para assistentes de voz. É projetado para cenários de conversação que são abertos, naturais ou híbridos dos dois com conclusão de tarefa ou uso de comando e controlo. Este alto grau de flexibilidade vem com uma maior complexidade, e cenários que são orientados para tarefas bem definidas usando a entrada de linguagem natural podem querer considerar [comandos personalizados (Pré-visualização)](custom-commands.md) para uma experiência de solução simplificada.

## <a name="getting-started-with-direct-line-speech"></a>Começar com discurso de linha direta

Os primeiros passos para criar um assistente de voz usando o Direct Line Speech são [obter uma chave de subscrição](get-started.md)de discurso, criar um novo bot associado a essa subscrição, e ligar o bot ao canal Direct Line Speech.

   ![Diagrama conceptual do fluxo de serviço de orquestração da linha direta](media/voice-assistants/overview-directlinespeech.png "O fluxo do Canal da Fala")

Para um guia completo e passo a passo sobre a criação de um simples assistente de voz usando o Discurso da Linha Direta, consulte [o tutorial para permitir o discurso do seu bot com o Speech SDK e o canal Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

Também oferecemos quickstarts projetados para que você esteja executando código e aprendendo as APIs rapidamente. Esta tabela inclui uma lista de quickstarts assistentes de voz organizados por linguagem e plataforma.

| Início Rápido | Plataforma | Referência da API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| Java | Janelas, macOS, Linux | [Procurar](https://aka.ms/csspeech/javaref) |
| Java | Android | [Procurar](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de amostra para a criação de um assistente de voz está disponível no GitHub. Estas amostras cobrem o pedido do cliente para a ligação ao seu assistente em várias línguas de programação populares.

* [Amostras de assistente de voz (SDK)](https://aka.ms/csspeech/samples)
* [Tutorial: Voz habilita o seu assistente com o SDK de Fala,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personalização

Os assistentes de voz construídos com o serviço Da Fala podem usar toda a gama de opções de personalização disponíveis para [o discurso-a-texto,](speech-to-text.md) [texto-a-fala](text-to-speech.md)e [seleção de palavras-chave personalizadas.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> As opções de personalização variam de forma linguística/local (ver [idiomas suportados).](supported-languages.md)

O Discurso da Linha Direta e a sua funcionalidade associada aos assistentes de voz são um suplemento ideal para a [Solução Assistente Virtual e modelo de empresa.](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) Embora o Direct Line Speech possa funcionar com qualquer bot compatível, estes recursos fornecem uma linha de base reutilizável para experiências de conversação de alta qualidade, bem como habilidades e modelos de suporte comuns para começar rapidamente.

## <a name="reference-docs"></a>Doutorados de referência

* [SDK de Voz](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Passos seguintes

* [Obtenha uma chave de subscrição do serviço Speech gratuitamente](get-started.md)
* [Obtenha o SDK do Discurso](speech-sdk.md)
* [Create and deploy a basic bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0) (Criar e implementar um bot básico)
* [Obtenha a solução de assistente virtual e o modelo de empresa](https://github.com/Microsoft/AI)

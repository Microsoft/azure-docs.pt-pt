---
title: Perguntas frequentes sobre os assistentes de voz
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas mais populares sobre assistentes de voz usando comandos personalizados (versão prévia) ou o canal de fala de linha direta.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 7ad3f932e9a10723d6cc1bae2fc4854c932d4c64
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507079"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Assistentes de voz: perguntas frequentes

Se você não encontrar respostas para suas perguntas neste documento, confira [outras opções de suporte](support.md).

## <a name="general"></a>Geral

**P: o que é um assistente de voz?**

**R:** Como o Cortana, um assistente de voz é uma solução que escuta o declarações falado de um usuário, analisa o conteúdo desses declarações para saber, executa uma ou mais ações em resposta à intenção do expressão e, em seguida, fornece uma resposta ao usuário que geralmente inclui um componente falado. É uma experiência de "entrada de voz e saída" para interagir com um sistema. os autores do assistente de voz criam um aplicativo no dispositivo usando o `DialogServiceConnector` no SDK de fala para se comunicar com um assistente criado usando [comandos personalizados (versão prévia)](custom-commands.md) ou o canal de [fala de linha direta](direct-line-speech.md) da estrutura de bot. Esses assistentes podem usar palavras-chave personalizadas, fala personalizada e voz personalizada para fornecer uma experiência adaptada à sua marca ou produto.

**P: devo usar comandos personalizados (visualização) ou a fala de linha direta? Qual é a diferença?**

**R:** os [comandos personalizados (versão prévia)](custom-commands.md) são um conjunto de ferramentas de menor complexidade para criar e hospedar facilmente um assistente que é bem adequado para cenários de conclusão de tarefas. A [Direct line Speech](direct-line-speech.md) fornece recursos mais avançados e mais sofisticados que podem permitir cenários de conversação robustos. Consulte a [comparação de soluções do assistente](voice-assistants.md#comparing-assistant-solutions) para obter mais informações.

**P: Introdução Como fazer?**

**R:** A melhor maneira de começar a criar um aplicativo de comandos personalizados (versão prévia) ou bot de estrutura de bot básico.

* [Criar um aplicativo de comandos personalizados (versão prévia)](quickstart-custom-speech-commands-create-new.md)
* [Criar um bot de estrutura de bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Conectar um bot ao canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Depurar

**P: recebo um erro 401 ao se conectar e nada funciona. Sei que minha chave de assinatura de fala é válida. O que está acontecendo?**

**R:** Ao gerenciar sua assinatura no portal do Azure, verifique se você está usando o recurso de **fala** (Microsoft. CognitiveServicesSpeechServices, "fala") e *não* o recurso de **Serviços cognitivas** ( Microsoft. CognitiveServicesAllInOne, "todos os serviços cognitivas"). Além disso, verifique o [suporte de região do serviço de fala para assistentes de voz](regions.md#voice-assistants).

![assinatura correta para a Direct line Speech](media/voice-assistants/faq-supported-subscription.png "exemplo de uma assinatura de fala compatível")

**P: obtenho o texto de reconhecimento do meu `DialogServiceConnector`, mas vejo um erro ' 1011 ' e nada do meu bot. Por?**

**R:** Esse erro indica um problema de comunicação entre o assistente e o serviço de assistente de voz.

* Para comandos personalizados (versão prévia), verifique se o aplicativo de comandos personalizados (versão prévia) foi publicado
* Para a Direct line Speech, verifique se você [conectou o bot ao canal de fala de linha direta](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [adicionou suporte de protocolo de streaming](https://aka.ms/botframework/addstreamingprotocolsupport) ao bot (com o suporte de soquete da Web relacionado) e, em seguida, verifique se o bot está respondendo às solicitações de entrada do canal.

**P: esse código ainda não funciona e/ou estou recebendo um erro diferente ao usar um `DialogServiceConnector`. O que devo fazer?**

**R:** O log baseado em arquivo fornece substancialmente mais detalhes e pode ajudar a acelerar as solicitações de suporte. Para habilitar essa funcionalidade, consulte [como usar o log de arquivos](how-to-use-logging.md).

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas](troubleshooting.md)
* [Notas de versão](releasenotes.md)

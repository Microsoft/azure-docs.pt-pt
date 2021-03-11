---
title: Assistentes de voz frequentemente fazem perguntas
titleSuffix: Azure Cognitive Services
description: Obtenha respostas às perguntas mais populares sobre assistentes de voz usando Comandos Personalizados ou o canal De Fala de Linha Direta.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 511eb12df511fd037fc0b5bec701c0cc5c29bad2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617782"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Assistentes de voz frequentemente fazem perguntas

Se não conseguir encontrar respostas às suas perguntas neste documento, consulte [outras opções de suporte](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Geral

**Q: O que é um assistente de voz?**

**A:** Tal como cortana, um assistente de voz é uma solução que ouve as declarações faladas de um utilizador, analisa o conteúdo dessas expressões para significado, executa uma ou mais ações em resposta à intenção da expressão, e depois fornece uma resposta ao utilizador que muitas vezes inclui um componente falado. É uma experiência de "voice-in, voice-out" para interagir com um sistema. Os autores assistentes de voz criam uma aplicação on-device utilizando `DialogServiceConnector` o SDK de voz para comunicar com um assistente criado usando [Comandos Personalizados](custom-commands.md) ou o canal de fala de [linha direta](direct-line-speech.md) do Quadro bot. Estes assistentes podem usar palavras-chave personalizadas, discurso personalizado e voz personalizada para proporcionar uma experiência à medida da sua marca ou produto.

**P: Devo usar comandos personalizados ou discurso de linha direta? Qual é a diferença?**

**R:** [Comandos Personalizados](custom-commands.md) é um conjunto de ferramentas de menor complexidade para facilmente criar e hospedar um assistente que é adequado para cenários de conclusão de tarefas. [O Direct Line Speech](direct-line-speech.md) oferece capacidades mais ricas e sofisticadas que podem permitir cenários de conversação robustos. Consulte a [comparação de soluções de assistente](voice-assistants.md#choosing-an-assistant-solution) para obter mais informações.

**P: Como é que começo?**

**A:** A melhor maneira de começar com a criação de uma aplicação de Comandos Personalizados (Pré-visualização) ou bot de estrutura de bot básico.

- [Criar uma aplicação de Comandos Personalizados (Pré-visualização)](./quickstart-custom-commands-application.md)
- [Criar um bot de estrutura de bot básico](/azure/bot-service/bot-builder-tutorial-basic-deploy)
- [Ligue um bot ao canal Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Depurar

**P: Onde está o meu segredo do canal?**

**A:** Se usou a versão de pré-visualização do Direct Line Speech ou está a ler documentação relacionada com a leitura, pode esperar encontrar uma chave secreta na página de registo do canal Direct Speech. O método de fábrica v1.7 `DialogServiceConfig` `FromBotSecret` no Speech SDK também espera este valor.

A versão mais recente do Direct Line Speech simplifica o processo de contacto com o seu bot a partir de um dispositivo. Na página de registo do canal, a queda no topo associa o seu registo do canal Direct Line Speech com um recurso de fala. Uma vez associado, o V1.8 Speech SDK inclui um `BotFrameworkConfig::FromSubscription` método de fábrica que configurará um para contactar o bot que `DialogServiceConnector` associou à sua subscrição.

Se ainda estiver a migrar a sua aplicação de cliente de v1.7 para v1.8, `DialogServiceConfig::FromBotSecret` pode continuar a trabalhar com um valor não vazio e não nulo para o seu parâmetro secreto do canal, por exemplo, o segredo anterior que usou. Será simplesmente ignorado quando utilizar uma subscrição de discurso associada a um registo de canais mais recente. Por favor, note que o valor _deve_ ser não nulo e não vazio, uma vez que estes são verificados no dispositivo antes que a associação do lado do serviço seja relevante.

Para obter um guia mais detalhado, consulte a [secção tutorial](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) que percorre o registo do canal.

**P: Eu tenho um erro 401 ao ligar e nada funciona. Sei que a minha chave de assinatura de discurso é válida. O que é que se passa?**

**A:** Ao gerir a sua subscrição no portal Azure, certifique-se de que está a utilizar o recurso **Speech** (Microsoft.CognitiveServicesSpeechServices, "Speech") e _não_ o recurso **Serviços Cognitivos** (Microsoft.CognitiveServicesAllInOne, "Todos os Serviços Cognitivos"). Além disso, verifique o [suporte da região do serviço de fala para assistentes de voz](regions.md#voice-assistants).

![subscrição correta para discurso de linha direta](media/voice-assistants/faq-supported-subscription.png "exemplo de uma subscrição compatível do Discurso")

**P: Recebo o meu texto de reconhecimento de volta do meu `DialogServiceConnector` , mas vejo um erro '1011' e nada do meu bot. Porquê?**

**A:** Este erro indica um problema de comunicação entre o seu assistente e o serviço de assistente de voz.

- Para comandos personalizados, certifique-se de que a sua Aplicação de Comandos Personalizados é publicada
- Para o Discurso de Linha Direta, certifique-se de que [ligou o seu bot ao canal Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech), adicione suporte ao protocolo de [streaming](/azure/bot-service/directline-speech-bot) ao seu bot (com o suporte de tomada web relacionado) e, em seguida, verifique se o seu bot está a responder às solicitações recebidas do canal.

**P: Este código ainda não funciona e/ou estou a ter um erro diferente ao usar um `DialogServiceConnector` . O que devo fazer?**

**A:** A sessão por ficheiros fornece substancialmente mais detalhes e pode ajudar a acelerar os pedidos de suporte. Para ativar esta funcionalidade, consulte [como utilizar a sessão de registo de ficheiros](how-to-use-logging.md).

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)
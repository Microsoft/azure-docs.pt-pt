---
title: Assistentes de voz frequentemente fazem perguntas
titleSuffix: Azure Cognitive Services
description: Obtenha respostas para as perguntas mais populares sobre assistentes de voz usando Comandos Personalizados (Pré-visualização) ou o canal de Discurso da Linha Direta.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74110353"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Assistentes de voz frequentemente fazem perguntas

Se não encontrar respostas às suas perguntas neste documento, confira [outras opções](support.md)de suporte .

## <a name="general"></a>Geral

**P: O que é um assistente de voz?**

**A:** Tal como cortana, um assistente de voz é uma solução que ouve as expressões faladas de um utilizador, analisa o conteúdo dessas expressões por significado, executa uma ou mais ações em resposta à intenção da expressão, e depois fornece uma resposta ao utilizador que muitas vezes inclui um componente falado. É uma experiência de "voice-in, voice-out" para interagir com um sistema. Os autores assistentes de voz `DialogServiceConnector` criam uma aplicação no dispositivo usando o SDK de Fala para comunicar com um assistente criado usando [comandos personalizados (pré-visualização)](custom-commands.md) ou o canal de discurso de [linha direta](direct-line-speech.md) do Quadro Bot. Estes assistentes podem usar palavras-chave personalizadas, discurso personalizado e voz personalizada para proporcionar uma experiência personalizada à sua marca ou produto.

**P: Devo usar comandos personalizados (pré-visualização) ou discurso de linha direta? Qual é a diferença?**

**R:** [Comandos Personalizados (Pré-visualização)](custom-commands.md) é um conjunto de ferramentas de menor complexidade para criar e hospedar facilmente um assistente que é adequado para cenários de conclusão de tarefas. [O Direct Line Speech](direct-line-speech.md) fornece capacidades mais ricas e sofisticadas que podem permitir cenários de conversação robustos. Consulte a [comparação de soluções assistentes](voice-assistants.md#choosing-an-assistant-solution) para mais informações.

**P: Como posso começar?**

**A:** A melhor maneira de começar com a criação de uma aplicação de Comandos Personalizados (Pré-visualização) ou bot bot basic Bot Framework.

- [Criar uma aplicação de Comandos Personalizados (Pré-visualização)](quickstart-custom-speech-commands-create-new.md)
- [Criar um bot quadro bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Ligue um bot ao canal direct line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Depurar

**P: Onde está o meu canal secreto?**

**A:** Se usou a versão de pré-visualização do Direct Line Speech ou está a ler documentação relacionada, pode esperar encontrar uma chave secreta na página de registo do canal Direct Line Speech. O método `FromBotSecret` de `DialogServiceConfig` fábrica v1.7 no SDK de Fala também espera este valor.

A versão mais recente do Direct Line Speech simplifica o processo de contacto com o seu bot a partir de um dispositivo. Na página de registo do canal, a queda no topo associa o registo do canal Direct Line Speech com um recurso de fala. Uma vez associado, o V1.8 Speech `BotFrameworkConfig::FromSubscription` SDK inclui `DialogServiceConnector` um método de fábrica que configurará um para contactar o bot que associou à sua subscrição.

Se ainda estiver a migrar o seu pedido de cliente de `DialogServiceConfig::FromBotSecret` v1.7 para v1.8, pode continuar a trabalhar com um valor não vazio e não nulo para o seu parâmetro secreto do canal, por exemplo, o segredo anterior que usou. Será simplesmente ignorado quando utilizar uma subscrição de discurso associada a um registo de um canal mais recente. Por favor, note que o valor _deve_ ser não nulo e não vazio, uma vez que estes são verificados no dispositivo antes que a associação do lado do serviço seja relevante.

Para obter um guia mais detalhado, consulte a [secção tutorial](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) que percorre a inscrição do canal.

**P: Tenho um erro 401 quando ligo e nada funciona. Sei que a minha chave de assinatura de discurso é válida. O que é que se passa?**

**A:** Ao gerir a sua subscrição no portal Azure, certifique-se de que está a usar o recurso **Speech** (Microsoft.CognitiveServicesSpeechServices, "Speech") e _não_ o recurso **Dos Serviços Cognitivos** (Microsoft.CognitiveServicesAllInOne, "All Cognitive Services"). Além disso, consulte o apoio da região de [serviço da Fala para assistentes](regions.md#voice-assistants)de voz .

![subscrição correta para discurso de linha direta](media/voice-assistants/faq-supported-subscription.png "exemplo de uma subscrição compatível do Discurso")

**P: Recebo texto de `DialogServiceConnector`reconhecimento do meu , mas vejo um erro '1011' e nada do meu bot. Porquê?**

**A:** Este erro indica um problema de comunicação entre o seu assistente e o serviço de assistente de voz.

- Para comandos personalizados (Pré-visualização), certifique-se de que a sua aplicação de comandos personalizados (pré-visualização) é publicada
- Para o Direct Line Speech, certifique-se de que [ligou o seu bot ao canal Direct Line Speech,](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)adicione suporte de protocolo de [streaming](https://aka.ms/botframework/addstreamingprotocolsupport) ao seu bot (com o suporte relacionado da Web Socket) e, em seguida, verifique se o seu bot está a responder aos pedidos de entrada do canal.

**P: Este código ainda não funciona e/ou estou a ter `DialogServiceConnector`um erro diferente ao usar um . O que devo fazer?**

**A:** A exploração madeireira baseada em ficheiros fornece substancialmente mais detalhes e pode ajudar a acelerar os pedidos de apoio. Para ativar esta funcionalidade, consulte [como utilizar o registo de ficheiros](how-to-use-logging.md).

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas](troubleshooting.md)
- [Notas de versão](releasenotes.md)

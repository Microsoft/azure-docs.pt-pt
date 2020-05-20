---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.author: diberry
ms.openlocfilehash: 1c3631b4a2964c5e3a8d8267d1934a5822966342
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673446"
---
Os Serviços Cognitivos fornecem dois serviços de processamento de linguagem natural, [compreensão linguística](../luis/what-is-luis.md) e Fabricante de [QnA,](../qnamaker/overview/overview.md)cada um com um propósito diferente. Entenda quando usar cada serviço e como se elogiam mutuamente.

O processamento de linguagem natural (NLP) permite que a aplicação do seu cliente, como um chat bot, trabalhe com os seus utilizadores, utilizando linguagem natural. Um utilizador introduz uma frase ou frase. O texto do utilizador pode ter má gramática, ortografia e pontuação. O Serviço Cognitivo pode trabalhar através da frase do utilizador de qualquer forma, devolvendo informações que o chat bot precisa para ajudar o utilizador.

## <a name="cognitive-services-with-nlp"></a>Serviços Cognitivos com NLP

A Compreensão da Linguagem (LUIS) e o Fabricante qnA fornecem NLP. A aplicação do cliente apresenta texto de linguagem natural. O serviço pega no texto, processa-o e devolve um resultado.

## <a name="when-to-use-each-service"></a>Quando utilizar cada serviço

A Compreensão da Linguagem (LUIS) e o Fabricante qnA resolvem diferentes questões. Luis determina a intenção do texto de um utilizador (conhecido como uma expressão), enquanto o QnA Maker determina a resposta ao texto de um utilizador (conhecido como consulta).

Para escolher o serviço correto, é necessário compreender o texto do utilizador proveniente da aplicação do cliente e que informações a aplicação do cliente precisa de obter do Serviço Cognitivo.

Se o seu chat bot receber o `How do I get to the Human Resources building on the Seattle North campus?` texto, utilize a ficha abaixo para entender como cada serviço funciona com o texto.

|Serviço|A aplicação do cliente determina|
|--|--|
|LUIS|**Determina** a intenção do utilizador de texto - o serviço não devolve a resposta à pergunta. Por exemplo, este texto é classificado como correspondendo à `FindLocation` intenção.<br>|
|QnA Maker|**Devolve a resposta à pergunta a** partir de uma base de conhecimento personalizada. Por exemplo, este texto é determinado como uma pergunta com a resposta estática do texto de `Get on the #9 bus and get off at Franklin street` .|
|||

> [!div class="mx-imgBorder"]
> ![Infográfico para determinar quando usar LUIS e quando usar o Fabricante QnA](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>Quando usa o LUIS?

Use LUIS quando precisa de saber a intenção da expressão como parte de um processo no chat bot. Continuando com o texto de exemplo, assim que souber que `How do I get to the Human Resources building on the Seattle North campus?` a intenção do utilizador é encontrar um local, pode passar detalhes sobre a expressão (retirada com entidades) para outro serviço, como um servidor de transporte, para obter a resposta.

Não é preciso combinar LUIS e QnA Maker para determinar a intenção.

Poderá combinar os dois serviços para esta expressão, se o chat bot precisar de processar o texto com base em intenções e entidades (usando LUIS) bem como encontrar a resposta de texto estática específica (utilizando o QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Quando usa o Fabricante de QnA?

Use o Fabricante QnA quando tiver uma base de conhecimento estática de respostas. Esta base de conhecimento é personalizada às suas necessidades, que construiu com documentos como PDFs e URLs.

Continuando com a expressão do exemplo, `How do I get to the Human Resources building on the Seattle North campus?` envie o texto, como uma consulta, para o seu serviço QnA Maker publicado e receba a melhor resposta.

Não é preciso combinar LUIS e QnA Maker para determinar a resposta à pergunta.

Poderá combinar os dois serviços para esta expressão, se o chat bot precisar de processar o texto com base em intenções e entidades (usando LUIS) bem como encontrar a resposta (usando o QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Utilize ambos os serviços quando a sua base de conhecimentos estiver incompleta

Se estiver a construir a sua base de conhecimentos qnA Maker, mas sabe que o domínio do assunto está a mudar (como informação oportuna), pode combinar os serviços LUIS e QnA Maker. Isto permite-lhe utilizar a informação na sua base de conhecimentos, mas também utilizar o LUIS para determinar a intenção do utilizador. Uma vez que o pedido do cliente tenha a intenção, pode solicitar informações relevantes de outra fonte.

A sua aplicação de cliente teria de monitorizar as respostas da LUIS e da QnA Maker para obter pontuações. Se a pontuação da QnA Maker estiver abaixo de algum limiar arbitrário, utilize as informações de intenção e entidade devolvidas da LUIS para passar a informação para um serviço de terceiros.

Continuando com o texto de exemplo, `How do I get to the Human Resources building on the Seattle North campus?` suponha que o QnA Maker devolve uma pontuação de baixa confiança. Utilize a intenção devolvida da LUIS, `FindLocation` e quaisquer entidades extraídas, tais como `Human Resources building` `Seattle North campus` e, para enviar esta informação para um serviço de mapeamento ou pesquisa para outra resposta.

Pode apresentar esta resposta de terceiros ao utilizador para validação. Assim que tiver a aprovação do utilizador, pode voltar ao QnA Maker para adicionar a informação para aumentar os seus conhecimentos.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Use ambos os serviços quando o seu chat bot precisar de mais informações

Se o seu chat bot precisar de mais informações do que qualquer um dos serviços, para continuar através de uma árvore de decisão, use tanto os serviços como processe ambas as respostas na aplicação do cliente.

Utilize a ferramenta Bot **[Dispatch CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** para ajudar a construir um processo para trabalhar com ambos os serviços. Esta ferramenta constrói uma aplicação de intenções de TOPO LUIS que despacha entre LUIS e QnA Maker como aplicações infantis. [Saiba mais](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=cs) sobre a integração com a estrutura LUIS, QnA Maker e Bot.

Utilize a amostra de construtor bot, **NLP com despacho**, em [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) ou [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), para implementar este tipo de chat bot.

## <a name="best-practices"></a>Melhores práticas

Implementar as melhores práticas para cada serviço:

* [Boas](../luis/luis-concept-best-practices.md) práticas luis
* [QnA Maker](../qnamaker/concepts/best-practices.md) boas práticas

## <a name="see-also"></a>Ver também

* [Compreensão de Idiomas (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Despacho CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Amostras de quadro bot](https://github.com/Microsoft/BotBuilder-Samples)
* [Serviço de botazur Azure](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Emulador de bot sinuoso azure](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot framework web chat](https://github.com/microsoft/BotFramework-WebChat)
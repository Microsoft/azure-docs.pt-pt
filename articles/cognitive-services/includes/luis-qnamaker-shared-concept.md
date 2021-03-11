---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: b2b405e7f6babf84ef8297310a5a7a34c3f2dc11
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623546"
---
Os Serviços Cognitivos fornecem dois serviços naturais de processamento de linguagem, [Compreensão linguística](../luis/what-is-luis.md) e [QnA Maker,](../qnamaker/overview/overview.md)cada um com um propósito diferente. Entenda quando usar cada serviço e como se elogiam mutuamente.

O processamento de linguagem natural (NLP) permite que a aplicação do seu cliente, como um chat bot, trabalhe com os seus utilizadores, utilizando linguagem natural. Um utilizador introduz uma frase ou frase. O texto do utilizador pode ter má gramática, ortografia e pontuação. O Serviço Cognitivo pode trabalhar através da frase do utilizador de qualquer maneira, devolvendo informações que o bot de chat precisa para ajudar o utilizador.

## <a name="cognitive-services-with-nlp"></a>Serviços Cognitivos com PNL

A Compreensão linguística (LUIS) e o Fabricante QnA fornecem NLP. A aplicação do cliente apresenta texto de linguagem natural. O serviço pega no texto, processa-o e devolve-lhe um resultado.

## <a name="when-to-use-each-service"></a>Quando usar cada serviço

A Compreensão linguística (LUIS) e o Criador QnA resolvem diferentes problemas. LUIS determina a intenção do texto de um utilizador (conhecido como uma expressão), enquanto o QnA Maker determina a resposta ao texto de um utilizador (conhecido como consulta).

Para escolher o serviço correto, é necessário compreender o texto do utilizador proveniente da aplicação do cliente e que informação a aplicação do cliente precisa de obter do Serviço Cognitivo.

Se o seu chat bot receber o `How do I get to the Human Resources building on the Seattle North campus?` texto, use o gráfico abaixo para entender como cada serviço funciona com o texto.

|Serviço|A aplicação do cliente determina|
|--|--|
|LUIS|**Determina a intenção do utilizador** de enviar texto - o serviço não devolve a resposta à pergunta. Por exemplo, este texto é classificado como correspondendo à `FindLocation` intenção.<br>|
|Criador de FAQ|**Devolve a resposta à pergunta a** partir de uma base de conhecimento personalizado. Por exemplo, este texto é determinado como uma pergunta com a resposta estática de texto de  `Get on the #9 bus and get off at Franklin street` .|
|||

> [!div class="mx-imgBorder"]
> ![Infográfico para determinar quando usar LUIS e quando usar O Criador QnA](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>Quando é que usas o LUIS?

Utilize o LUIS quando precisar de saber a intenção da expressão como parte de um processo no bot de chat. Continuando com o texto exemplo, `How do I get to the Human Resources building on the Seattle North campus?` , uma vez que você sabe que a intenção do utilizador é encontrar uma localização, você pode passar detalhes sobre a expressão (retirado com entidades) para outro serviço, como um servidor de transporte, para obter a resposta.

Não é preciso combinar LUIS e QnA Maker para determinar a intenção.

Você pode combinar os dois serviços para esta expressão, se o bot de chat precisar processar o texto com base em intenções e entidades (usando LUIS) bem como encontrar a resposta de texto estática específica (usando QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Quando é que usas o QnA Maker?

Utilize o Criador de FAQ quando tiver uma base de dados de conhecimento estática de respostas. Esta base de dados de conhecimento é personalizada de acordo com as suas necessidades, que criou com documentos como PDFs e URLs.

Continuando com o exemplo de expressão, `How do I get to the Human Resources building on the Seattle North campus?` envie o texto, como consulta, para o seu serviço QnA Maker publicado e receba a melhor resposta.

Não é preciso combinar LUIS e QnA Maker para determinar a resposta à pergunta.

Você pode combinar os dois serviços para esta expressão, se o chat bot precisar processar o texto com base em intenções e entidades (usando LUIS) bem como encontrar a resposta (usando QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Use ambos os serviços quando a sua base de conhecimento estiver incompleta

Se estiver a construir a sua base de conhecimentos QnA Maker, mas souber que o domínio do assunto está a mudar (como informações oportunas), pode combinar os serviços LUIS e QnA Maker. Isto permite-lhe utilizar a informação na sua base de conhecimento, mas também utilizar o LUIS para determinar a intenção de um utilizador. Uma vez que o pedido do cliente tenha a intenção, pode solicitar informações relevantes de outra fonte.

A sua aplicação ao cliente teria de monitorizar as respostas da LUIS e da QnA Maker para obter pontuações. Se a pontuação da QnA Maker estiver abaixo de algum limiar arbitrário, utilize as informações de intenção e entidade devolvidas da LUIS para passar a informação para um serviço de terceiros.

Continuando com o texto exemplo, `How do I get to the Human Resources building on the Seattle North campus?` suponha que o QnA Maker devolve uma pontuação de baixa confiança. Utilize a intenção devolvida da LUIS, `FindLocation` bem como de quaisquer entidades extraídas, tais como `Human Resources building` `Seattle North campus` e, para enviar esta informação para um serviço de mapeamento ou pesquisa para outra resposta.

Pode apresentar esta resposta de terceiros ao utilizador para validação. Uma vez que tenha a aprovação do utilizador, pode voltar ao QnA Maker para adicionar a informação para aumentar o seu conhecimento.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Use ambos os serviços quando o seu chat bot precisa de mais informações

Se o seu chat bot precisar de mais informações do que qualquer um dos serviços fornece, para continuar através de uma árvore de decisão, use ambos os serviços e processe ambas as respostas na aplicação do cliente.

Utilize a ferramenta **[CoLI de despacho de](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** despacho de bot para ajudar a construir um processo para trabalhar com ambos os serviços. Esta ferramenta constrói uma app de intenções LUIS de topo que despacha entre a LUIS e a QnA Maker como aplicações infantis. [Saiba mais](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs) sobre a integração com a estrutura LUIS, QnA Maker e Bot.

Utilize a amostra de construtor bot, **NLP com despacho,** em [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) ou [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), para implementar este tipo de chat bot.

## <a name="best-practices"></a>Melhores práticas

Implementar as melhores práticas para cada serviço:

* [Boas](../luis/luis-concept-best-practices.md) práticas do LUIS
* [QnA Maker](../qnamaker/concepts/best-practices.md) as melhores práticas

## <a name="see-also"></a>Ver também

* [Compreensão de Idiomas (LUIS)](../luis/what-is-luis.md)
* [Criador de FAQ](../qnamaker/overview/overview.md)
* [Despacho CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Amostras de estrutura de bot](https://github.com/Microsoft/BotBuilder-Samples)
* [Serviço de bots Azure](/azure/bot-service/bot-service-overview-introduction)
* [Emulador de bots Azure](https://github.com/Microsoft/BotFramework-Emulator)
* [Chat web de estrutura de bot](https://github.com/microsoft/BotFramework-WebChat)
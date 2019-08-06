---
title: Usar serviços cognitivas do NLP para enriquecer conversas
titlesuffix: Azure Cognitive Services
description: Os serviços cognitivas fornecem dois serviços de processamento de idioma natural, Reconhecimento vocal e QnA Maker, cada um com uma finalidade diferente. Entenda quando usar cada serviço e como eles se complementam.
author: diberry
ms.author: diberry
manager: nitinme
ms.topic: conceptual
ms.service: cognitive-services
ms.date: 08/01/2019
ms.openlocfilehash: f293f57f4a98e822aa1c3950614ba5a186f9751d
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816927"
---
# <a name="use-cognitive-services-with-natural-language-processing-nlp-to-enrich-bot-conversations"></a>Use serviços cognitivas com NLP (processamento de idioma natural) para enriquecer conversas de bot

Os serviços cognitivas fornecem dois serviços de processamento de idioma natural, [reconhecimento vocal](what-is-luis.md) e [QnA Maker](../qnamaker/overview/overview.md), cada um com uma finalidade diferente. Entenda quando usar cada serviço e como eles se complementam. 

O NLP (processamento de idioma natural) permite que seu aplicativo cliente, como um bot de chat, trabalhe com seus usuários, usando linguagem natural. Um usuário insere uma frase ou frase. O texto do usuário pode ter más gramática, ortografia e pontuação. O serviço cognitiva pode funcionar na frase do usuário de qualquer forma, retornando informações de que o bot chat precisa para ajudar o usuário. 

## <a name="cognitive-services-with-nlp"></a>Serviços cognitivas com NLP

Reconhecimento vocal (LUIS) e QnA Maker fornecer NLP. O aplicativo cliente envia um texto de idioma natural. O serviço usa o texto, processa-o e retorna um resultado. 

## <a name="when-to-use-each-service"></a>Quando usar cada serviço

Reconhecimento vocal (LUIS) e QnA Maker resolver problemas diferentes. LUIS determina a intenção do texto de um usuário (conhecido como um expressão), enquanto QnA Maker determina a resposta para o texto de um usuário (conhecido como consulta). 

Para escolher o serviço correto, você precisa entender o texto do usuário proveniente do aplicativo cliente e as informações que o aplicativo cliente precisa obter do serviço cognitiva.

Se o seu bot de chat receber `How do I get to the Human Resources building on the Seattle North campus?`o texto, use o gráfico abaixo para entender como cada serviço funciona com o texto.

|Serviço|O aplicativo cliente determina|
|--|--|
|LUIS|**Determina a intenção** de texto do usuário-o serviço não retorna a resposta para a pergunta. Por exemplo, esse texto é classificado como correspondente à `FindLocation` intenção.<br>|
|Criador de FAQ|**Retorna a resposta para a pergunta** de uma base de dados de conhecimento personalizada. Por exemplo, esse texto é determinado como uma pergunta com a resposta de texto estático `Get on the #9 bus and get off at Franklin street`de.|
|||

## <a name="when-do-you-use-luis"></a>Quando você usa o LUIS? 

Use LUIS quando precisar saber a intenção do expressão como parte de um processo no bot de bate-papo. Continuando com o texto de `How do I get to the Human Resources building on the Seattle North campus?`exemplo,, quando você sabe que a intenção do usuário é encontrar um local, você pode passar detalhes sobre o expressão (extraído com entidades) para outro serviço, como um servidor de transporte, para obter a resposta. 

Você não precisa combinar LUIS e QnA Maker para determinar a intenção. 

Você pode combinar os dois serviços para esse expressão, se o bot de chat precisar processar o texto com base em intenções e entidades (usando LUIS), bem como localizar a resposta de texto estático específica (usando QnA Maker).

## <a name="when-do-you-use-qna-maker"></a>Quando você usa QnA Maker? 

Use QnA Maker quando você tiver uma base de dados de conhecimento estática de respostas. Essa base de dados de conhecimento é personalizada para suas necessidades, que você criou com documentos como PDFs e URLs. 

Continuando com o exemplo expressão `How do I get to the Human Resources building on the Seattle North campus?`,, envie o texto, como uma consulta, para o serviço de QnA Maker publicado e receba a melhor resposta. 

Você não precisa combinar LUIS e QnA Maker para determinar a resposta para a pergunta.

Você pode combinar os dois serviços para esse expressão, se o bot de chat precisar processar o texto com base em intenções e entidades (usando LUIS), bem como encontrar a resposta (usando QnA Maker).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Usar ambos os serviços quando sua base de dados de conhecimento estiver incompleta

Se você estiver criando sua base de dados de conhecimento QnA Maker, mas souber que o domínio da entidade está mudando (como informações oportunas), poderá combinar os serviços LUIS e QnA Maker. Isso permite que você use as informações em sua base de dados de conhecimento, mas também use LUIS para determinar a intenção de um usuário. Depois que o aplicativo cliente tiver a intenção, ele poderá solicitar informações relevantes de outra fonte. 

O aplicativo cliente precisaria monitorar as respostas LUIS e QnA Maker para pontuações. Se a pontuação de QnA Maker estiver abaixo de um limite arbitrário, use as informações de intenção e entidade retornadas de LUIS para passar as informações para um serviço de terceiros.

Continuando com o texto de `How do I get to the Human Resources building on the Seattle North campus?`exemplo,, suponha que QnA Maker retorna uma pontuação de confiança baixa. Use a intenção retornada de Luis `FindLocation` e todas as entidades extraídas, `Human Resources building` como e `Seattle North campus`, para enviar essas informações a um serviço de mapeamento ou de pesquisa para outra resposta. 

Você pode apresentar essa resposta de terceiros ao usuário para validação. Depois de ter a aprovação do usuário, você pode voltar para QnA Maker para adicionar as informações para aumentar seu conhecimento. 

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Use ambos os serviços quando o seu bot de chat precisar de mais informações

Se o seu bot de chat precisar de mais informações do que o serviço fornece, para continuar em uma árvore de decisão, use ambos os serviços e processe ambas as respostas no aplicativo cliente. 

Use a ferramenta de **[expedição da CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** do bot Framework para ajudar a criar um processo para trabalhar com ambos os serviços. Essa ferramenta cria um aplicativo LUIS superior de tentativas que expedem entre LUIS e QnA Maker como aplicativos filho. 

Use o exemplo do bot Builder, **NLP com expedição**, [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) in ou [node. js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch), para implementar esse tipo de bot de chat. 

## <a name="best-practices"></a>Melhores práticas

Implemente as práticas recomendadas para cada serviço:

* Práticas recomendadas do [Luis](luis-concept-best-practices.md)
* Práticas recomendadas de [QnA Maker](../qnamaker/concepts/best-practices.md)

## <a name="see-also"></a>Consulte também

* [Language Understanding (LUIS)](what-is-luis.md)
* [Criador de FAQ](../qnamaker/overview/overview.md)
* [CLI de expedição](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Amostras do bot Framework](https://github.com/Microsoft/BotBuilder-Samples)
* [Serviço de bot do Azure](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
* [Emulador de bot do Azure](https://github.com/Microsoft/BotFramework-Emulator)
* [Chat da Web do bot Framework](https://github.com/microsoft/BotFramework-WebChat)
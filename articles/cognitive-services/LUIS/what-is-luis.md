---
title: O que é o Language Understanding (LUIS)?
titleSuffix: Azure Cognitive Services
description: O Language Understanding (LUIS) é um serviço de API baseado na cloud que aplica inteligência de machine learning personalizada aos textos conversacionais de idioma natural do utilizador, para prever o sentido geral e extrair informações relevantes e detalhadas.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/04/2019
ms.author: diberry
ms.openlocfilehash: 8ee22a2a8a12eb85439e191bc21e6cf391bea3f8
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612855"
---
# <a name="what-is-language-understanding-luis"></a>O que é o Language Understanding (LUIS)?

O Language Understanding (LUIS) é um serviço de API baseado na cloud que aplica inteligência de machine learning personalizada aos textos conversacionais de idioma natural do utilizador, para prever o sentido geral e extrair informações relevantes e detalhadas. 

Uma aplicação cliente para o LUIS é qualquer aplicação conversacional que comunique com um utilizador num idioma natural para concluir uma tarefa. Alguns exemplos de aplicações cliente incluem aplicações de redes sociais, bots de chat e aplicações para ambiente de trabalho com fala ativada.  

![Imagem conceitual de três aplicativos cliente trabalhando com Reconhecimento vocal de serviços cognitivas (LUIS)](./media/luis-overview/luis-entry-point.png "Imagem conceitual de três aplicativos cliente trabalhando com Reconhecimento vocal de serviços cognitivas (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Utilizar o LUIS num bot de chat

<a name="Accessing-LUIS"></a>

Depois que o aplicativo LUIS é publicado, um aplicativo cliente envia declarações (texto) para a [API][endpoint-apis] de ponto de extremidade de processamento de linguagem natural Luis e recebe os resultados como respostas JSON. Os bots de chat são uma aplicação cliente comum para o LUIS.


![Imagens conceituais do LUIS trabalhando com o chat bot para prever o texto do usuário com NLP (reconhecimento de linguagem natural)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Imagens conceituais do LUIS trabalhando com o chat bot para prever o texto do usuário com o reconhecimento de linguagem natural (NLP")

|Passo|Ação|
|:--|:--|
|1|A aplicação cliente envia a _expressão_ do utilizador (o texto nas palavras do utilizador), "I want to call my HR rep" (“Quero ligar ao meu representante de RH”) ao ponto final do LUIS como um pedido HTTP.|
|2|O LUIS permite que você crie seus modelos de linguagem personalizados para adicionar inteligência ao seu aplicativo. Os modelos de idioma conhecidos do computador pegam o texto de entrada não estruturado do usuário e retornam uma resposta formatada em JSON, com uma das principais intenções, `HRContact`. A resposta mínima do ponto final do JSON contém a expressão da consulta e a intenção com a melhor classificação. Ele também pode extrair dados como a entidade de _tipo de contato_ .|
|3|A aplicação cliente utiliza a resposta JSON para tomar decisões quanto à forma como satisfaz os pedidos do utilizador. Essas decisões podem incluir a árvore de decisão no código do bot Framework e chamadas para outros serviços. |

A aplicação LUIS proporciona inteligência para que a aplicação cliente possa tomar decisões informadas. O LUIS não fornece essas decisões. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Processamento de linguagem natural

Seu aplicativo LUIS contém um modelo de linguagem natural específico de domínio. Pode iniciar as aplicações LUIS com um modelo de domínio pré-criado, criar o seu próprio modelo ou misturar partes de um domínio pré-criado com as suas informações personalizadas.

* **Modelo pré-criado** O LUIS tem muitos modelos de domínio pré-criados, incluindo intenções, expressões e entidades pré-criadas. Pode utilizar as entidades pré-criadas sem utilizar as intenções e as expressões do modelo pré-criado. Os [modelos de domínio pré-criados](luis-how-to-use-prebuilt-domains.md) incluem todo o design para si e são uma excelente forma de começar a utilizar o LUIS rapidamente.

* **Modelo personalizado** O LUIS oferece várias maneiras de identificar seus próprios modelos personalizados, incluindo intenções e entidades. As entidades incluem entidades aprendidas por computador, entidades específicas ou literais e uma combinação de Machine-aprendited e literal.

## <a name="build-the-luis-model"></a>Compilar o modelo do LUIS
Crie o modelo com as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087) ou com o [portal do Luis](https://www.luis.ai).

O modelo do LUIS começa com categorias de intenções do utilizador, denominadas **[intenções](luis-concept-intent.md)** . Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do utilizador. Cada expressão pode fornecer dados que precisam ser extraídos. 

|Exemplo de expressão do utilizador|Intenção|Dados extraídos|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|ReservarBilhete|Seattle|
|`When does your store __open__?`|HoráriodaLojaeLocalização|abre|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|AgendarReunião|13:00, João|

## <a name="query-prediction-endpoint"></a>Ponto final de predição da consulta

Depois que o aplicativo é treinado e publicado no ponto de extremidade, o aplicativo cliente envia declarações para a API de [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) de previsão. A API aplica o aplicativo ao expressão para análise e responde com os resultados da previsão em um formato JSON.  

A resposta mínima do ponto final do JSON contém a expressão da consulta e a intenção com a melhor classificação. Ele também pode extrair dados como a seguinte entidade de **tipo de contato** e sentimentos gerais. 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>Melhorar a predição do modelo

Depois que seu aplicativo LUIS é publicado e recebe declarações de usuário real, LUIS fornece [aprendizado ativo](luis-concept-review-endpoint-utterances.md) de declarações de ponto de extremidade para melhorar a precisão da previsão. 

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento
O LUIS fornece ferramentas, controle de versão e colaboração com outros autores de LUIS para integrar o [ciclo de vida de desenvolvimento](luis-concept-app-iteration.md)completo. 

## <a name="implementing-luis"></a>Implementar o LUIS
Reconhecimento vocal (LUIS), como uma API REST, pode ser usada com qualquer produto, serviço ou estrutura com uma solicitação HTTP. A lista seguinte contém os principais produtos e serviços Microsoft que são utilizados com o LUIS.

A aplicação cliente principal do LUIS é:
* [Bot de aplicação Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) - cria rapidamente um bot de chat ativado para o LUIS, para falar com um utilizador através de introdução de texto. Usa o [bot Framework][bot-framework] versão [4. x](https://github.com/Microsoft/botbuilder-dotnet) para uma experiência de bot completa.

Ferramentas para utilizar o LUIS de forma rápida e fácil com um bot:
* [CLI do Luis](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) O pacote NPM fornece criação e previsão com o como uma ferramenta de linha de comando autônoma ou como importação. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen)– O LUISGen é uma ferramenta para gerar código fonte TypeScript e em C# com tipos de dados inflexíveis a partir de um modelo exportado do LUIS.
* O [Dispatch](https://aka.ms/dispatch-tool) permite utilizar uma variedade de aplicações do LUIS e de Criador de FAQ a partir de uma aplicação principal através da utilização do modelo de dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) – O LUDown é uma ferramenta de linha de comandos que ajuda a gerir modelos de linguagem para o bot.
* [Bot Framework-Composer](https://github.com/microsoft/BotFramework-Composer) -uma ferramenta de desenvolvimento integrada para desenvolvedores e equipes multidisciplinares para criar bots e experiências de conversa com o Microsoft bot Framework

Outros Serviços Cognitivos utilizados com o LUIS:
* [QnA Maker][qnamaker] permite que vários tipos de texto sejam combinados em uma base de dados de conhecimento de perguntas e respostas.
* [Speech service](../Speech-Service/overview.md) - converte os pedidos de idioma falado em texto. 
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) - permite-lhe criar conversas de bot mais depressa com o LUIS.

Exemplos usando LUIS:
* [Ai de conversa](https://github.com/Microsoft/AI) Repositório GitHub.
* [Bot Framework-amostras de bot](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Passos seguintes

* [Novidades](whats-new.md)
* Crie uma aplicação do LUIS nova com um domínio [pré-criado](luis-get-started-create-app.md) ou [personalizado](luis-quickstart-intents-only.md).
* [Consulte o ponto final da predição](luis-get-started-get-intent-from-browser.md) de uma aplicação IoT pública. 
* [Recursos para desenvolvedores](developer-reference-resource.md) para Luis. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

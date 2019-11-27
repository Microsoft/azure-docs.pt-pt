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
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456562"
---
# <a name="what-is-language-understanding-luis"></a>O que é o Language Understanding (LUIS)?

O Reconhecimento vocal (LUIS) é um serviço de API baseado em nuvem que aplica inteligência de aprendizado de máquina personalizada a texto em idioma natural para prever o significado geral e extrair informações relevantes e detalhadas. 

Por exemplo, quando um aplicativo cliente envia o texto, `find me a wireless keyboard for $30`, LUIS responde com o objeto JSON a seguir. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
No exemplo acima, a _**intenção**_ ou o significado geral da frase é que o usuário está tentando localizar um item. As partes detalhadas das informações que o LUIS extrai são chamadas de _**entidades**_ . Nesse caso, as entidades são o nome do item que o usuário está procurando e a quantidade de dinheiro que desejam gastar.

Os aplicativos cliente usam o JSON retornado LUIS, a _intenção_ (categoria) e _entidades_ (informações detalhadas extraídas) para gerar ações no aplicativo cliente. Um aplicativo cliente para LUIS é geralmente um aplicativo de conversação que se comunica com um usuário em linguagem natural para concluir uma tarefa. Alguns exemplos de aplicações cliente incluem aplicações de redes sociais, bots de chat e aplicações para ambiente de trabalho com fala ativada. 

![Imagem conceitual de três aplicativos cliente trabalhando com Reconhecimento vocal de serviços cognitivas (LUIS)](./media/luis-overview/luis-entry-point.png "Imagem conceitual de três aplicativos cliente trabalhando com Reconhecimento vocal de serviços cognitivas (LUIS)")

## <a name="example-use-luis-in-a-chat-bot"></a>Exemplo de uso de LUIS em um bot de chat

<a name="Accessing-LUIS"></a>

Um aplicativo cliente envia declarações (texto) para a [API][endpoint-apis] de ponto de extremidade de processamento de linguagem natural do Luis publicado e recebe os resultados como respostas JSON. Os bots de chat são uma aplicação cliente comum para o LUIS.


![Imagens conceituais do LUIS trabalhando com o chat bot para prever o texto do usuário com NLP (reconhecimento de linguagem natural)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Imagens conceituais do LUIS trabalhando com o chat bot para prever o texto do usuário com o reconhecimento de linguagem natural (NLP")

|Passo|Ação|
|:--|:--|
|1|A aplicação cliente envia a _expressão_ do utilizador (o texto nas palavras do utilizador), "I want to call my HR rep" (“Quero ligar ao meu representante de RH”) ao ponto final do LUIS como um pedido HTTP.|
|2|O LUIS aplica modelos de linguagem aprendidos ao computador ao texto de entrada não estruturado do usuário e retorna uma resposta formatada em JSON, com uma das principais intenções, `HRContact`. A resposta mínima do ponto final do JSON contém a expressão da consulta e a intenção com a melhor classificação. Ele também pode extrair dados como a entidade de _tipo de contato_ .|
|3|A aplicação cliente utiliza a resposta JSON para tomar decisões quanto à forma como satisfaz os pedidos do utilizador. Essas decisões podem incluir uma árvore de decisão no bot e chamadas para outros serviços. |

A aplicação LUIS proporciona inteligência para que a aplicação cliente possa tomar decisões informadas. O LUIS não fornece essas decisões. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Processamento de linguagem natural

Seu aplicativo LUIS contém modelos de linguagem natural específicos de domínio, que funcionam juntos. Você pode iniciar o aplicativo LUIS com um ou mais modelos predefinidos, criar seu próprio modelo ou misturar modelos predefinidos com suas próprias informações personalizadas.

* **Modelo predefinido** O LUIS tem muitos domínios pré-criados que incluem modelos de intenção e entidade que funcionam em conjunto para completar cenários de uso comuns. Esses domínios incluem rótulos de declarações que podem ser inspecionados e editados, permitindo que você os personalize. Os [modelos de domínio pré-criados](luis-how-to-use-prebuilt-domains.md) incluem todo o design para si e são uma excelente forma de começar a utilizar o LUIS rapidamente. Além disso, há entidades predefinidas, como moeda e número, que você pode usar independentemente dos domínios predefinidos.

* **Modelo personalizado** O LUIS oferece várias maneiras de criar seus próprios modelos personalizados, incluindo intenções e entidades. As entidades incluem entidades aprendidas por computador, entidades de correspondência de padrões e uma combinação de correspondência de padrões e aprendidas por máquina.

## <a name="build-the-luis-app"></a>Compilar o aplicativo LUIS
Compile o aplicativo com as APIs de [criação](https://go.microsoft.com/fwlink/?linkid=2092087) ou com o [portal do Luis](https://www.luis.ai).

O aplicativo LUIS começa com categorias de texto de entrada chamadas **[intenções](luis-concept-intent.md)** . Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do utilizador. Cada expressão pode fornecer dados que precisam ser extraídos. 

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

## <a name="iterative-development-lifecycle"></a>Ciclo de vida de desenvolvimento iterativo
O LUIS fornece ferramentas, controle de versão e colaboração com outros autores de LUIS para integrar o [ciclo de vida de desenvolvimento](luis-concept-app-iteration.md)iterativo completo. 

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
* Criar um novo aplicativo LUIS com um domínio [predefinido](luis-get-started-create-app.md) ou [personalizado](luis-quickstart-intents-only.md)
* [Consulte o ponto final da predição](luis-get-started-get-intent-from-browser.md) de uma aplicação IoT pública. 
* [Recursos para desenvolvedores](developer-reference-resource.md) para Luis. 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

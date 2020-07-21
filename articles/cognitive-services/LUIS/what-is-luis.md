---
title: O que é o Language Understanding (LUIS)?
description: O Language Understanding (LUIS) é um serviço de API baseado na cloud que aplica inteligência de machine learning personalizada aos textos conversacionais de idioma natural do utilizador, para prever o sentido geral e extrair informações relevantes e detalhadas.
ms.topic: overview
ms.date: 05/05/2020
ms.openlocfilehash: 231a6580a6776b82173865744e9e8757c2fa08f1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538042"
---
# <a name="what-is-language-understanding-luis"></a>O que é o Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Language Understanding (LUIS) é um serviço de API baseado na cloud que aplica inteligência de machine learning personalizada aos textos conversacionais de idioma natural do utilizador, para prever o sentido geral e extrair informações relevantes e detalhadas.

Uma aplicação cliente para o LUIS é qualquer aplicação conversacional que comunique com um utilizador num idioma natural para concluir uma tarefa. Alguns exemplos de aplicações cliente incluem aplicações de redes sociais, bots de chat e aplicações para ambiente de trabalho com fala ativada.

![Imagem conceptual de 3 aplicações de clientes trabalhando com Cognitive Services Language Understanding (LUIS)](./media/luis-overview/luis-entry-point.png "Imagem conceptual de 3 aplicações de clientes trabalhando com Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Utilizar o LUIS num bot de chat

<a name="Accessing-LUIS"></a>

Quando a aplicação LUIS for publicada, uma aplicação cliente envia expressões (texto) para a [API][endpoint-apis] de ponto final de processamento de idioma natural do LUIS e recebe os resultados como respostas JSON. Os bots de chat são uma aplicação cliente comum para o LUIS.


![Imagens conceptuais de LUIS trabalhando com chat bot para prever texto do utilizador com compreensão de linguagem natural (NLP)](./media/luis-overview/LUIS-chat-bot-request-response.svg "Imagens conceptuais de LUIS trabalhando com chat bot para prever texto do utilizador com compreensão de linguagem natural (NLP")

|Passo|Ação|
|:--|:--|
|1|A aplicação cliente envia a _expressão_ do utilizador (o texto nas palavras do utilizador), "I want to call my HR rep" (“Quero ligar ao meu representante de RH”) ao ponto final do LUIS como um pedido HTTP.|
|2|O LUIS permite-lhe criar os seus modelos de linguagem personalizados para adicionar inteligência à sua aplicação. Os modelos de linguagem aprendidos por máquinas pegam no texto de entrada não estruturado do utilizador e devolvem uma resposta formatada por JSON, com uma intenção superior, `HRContact` . A resposta mínima do ponto final do JSON contém a expressão da consulta e a intenção com a melhor classificação. Também pode extrair dados como a entidade _do Tipo de Contacto._|
|3|A aplicação cliente utiliza a resposta JSON para tomar decisões quanto à forma como satisfaz os pedidos do utilizador. Estas decisões podem incluir a árvore de decisão no código-quadro do bot e chamadas para outros serviços. |

A aplicação LUIS proporciona inteligência para que a aplicação cliente possa tomar decisões informadas. O LUIS não fornece essas decisões.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Processamento de linguagem natural

A sua aplicação LUIS contém um modelo de linguagem natural específico do domínio. Pode iniciar as aplicações LUIS com um modelo de domínio pré-criado, criar o seu próprio modelo ou misturar partes de um domínio pré-criado com as suas informações personalizadas.

* **Modelo pré-criado** O LUIS tem muitos modelos de domínio pré-criados, incluindo intenções, expressões e entidades pré-criadas. Pode utilizar as entidades pré-criadas sem utilizar as intenções e as expressões do modelo pré-criado. Os [modelos de domínio pré-criados](luis-how-to-use-prebuilt-domains.md) incluem todo o design para si e são uma excelente forma de começar a utilizar o LUIS rapidamente.

* **Modelo personalizado** A LUIS dá-lhe várias formas de identificar os seus próprios modelos personalizados, incluindo intenções e entidades. As entidades incluem entidades de aprendizagem automática, entidades específicas ou literais, e uma combinação de aprendizagem automática e literal.

## <a name="build-the-luis-model"></a>Compilar o modelo do LUIS
Construa o modelo com as APIs [de autoria](https://go.microsoft.com/fwlink/?linkid=2092087) ou com o [portal LUIS](https://www.luis.ai).

O modelo do LUIS começa com categorias de intenções do utilizador, denominadas **[intenções](luis-concept-intent.md)**. Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do utilizador. Cada expressão pode fornecer dados que precisam de ser extraídos.

|Exemplo de expressão do utilizador|Intenção|Dados extraídos|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|ReservarBilhete|Seattle|
|`When does your store open?`|HoráriodaLojaeLocalização|abre|
|`Schedule a meeting at 1pm with Bob in Distribution`|AgendarReunião|13:00, João|

## <a name="query-prediction-endpoint"></a>Ponto final de predição da consulta

Depois de a sua aplicação ser treinada e publicada no ponto final, a aplicação do cliente envia declarações para a previsão [de](https://go.microsoft.com/fwlink/?linkid=2092356) ponta final API. A API aplica o modelo à expressão para análise e responde com os resultados da previsão num formato JSON.

A resposta mínima do ponto final do JSON contém a expressão da consulta e a intenção com a melhor classificação. Também pode extrair dados como a seguinte entidade **do Tipo de Contacto** e o sentimento geral.

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

Após a publicação da sua aplicação LUIS e receber declarações reais de utilizador, a LUIS fornece [uma aprendizagem ativa](luis-concept-review-endpoint-utterances.md) das expressões de ponto final para melhorar a precisão da previsão.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Ciclo de vida de desenvolvimento
A LUIS fornece ferramentas, versões e colaboração com outros autores luis para integrar em todo o ciclo de vida de [desenvolvimento.](luis-concept-app-iteration.md)

## <a name="implementing-luis"></a>Implementar o LUIS
A Compreensão linguística (LUIS), como API REST, pode ser utilizada com qualquer produto, serviço ou enquadramento com um pedido HTTP. A lista seguinte contém os principais produtos e serviços Microsoft que são utilizados com o LUIS.

A aplicação cliente principal do LUIS é:
* [Bot de aplicação Web](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) - cria rapidamente um bot de chat ativado para o LUIS, para falar com um utilizador através de introdução de texto. Utiliza a versão [4.x](https://github.com/Microsoft/botbuilder-dotnet) [do Bot Framework][bot-framework] para uma experiência completa de bot.

Ferramentas para utilizar o LUIS de forma rápida e fácil com um bot:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) O pacote NPM fornece a autoria e previsão como uma ferramenta de linha de comando autónoma ou como importação.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen)– O LUISGen é uma ferramenta para gerar código fonte TypeScript e em C# com tipos de dados inflexíveis a partir de um modelo exportado do LUIS.
* O [Dispatch](https://aka.ms/dispatch-tool) permite utilizar uma variedade de aplicações do LUIS e de Criador de FAQ a partir de uma aplicação principal através da utilização do modelo de dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown é uma ferramenta de linha de comando que ajuda a gerir modelos de linguagem para o seu bot.
* [Bot framework - Composer](https://github.com/microsoft/BotFramework-Composer) - uma ferramenta integrada de desenvolvimento para desenvolvedores e equipas multidisciplinares para construir bots e experiências de conversação com o Microsoft Bot Framework

Outros Serviços Cognitivos utilizados com o LUIS:
* [Criador de FAQ][qnamaker] - permite combinar vários tipos de textos numa base de conhecimento de perguntas e respostas.
* [Speech service](../Speech-Service/overview.md) - converte os pedidos de idioma falado em texto.
* [Conversation Learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) - permite-lhe criar conversas de bot mais depressa com o LUIS.

Amostras com LUIS:
* [IA conversacional](https://github.com/Microsoft/AI) Repositório gitHub.
* [Estrutura bot - Amostras de bot](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>Próximos passos

* [Novidades](whats-new.md)
* Crie uma aplicação do LUIS nova com um domínio [pré-criado](luis-get-started-create-app.md) ou [personalizado](luis-quickstart-intents-only.md).
* [Consulte o ponto final da predição](luis-get-started-get-intent-from-browser.md) de uma aplicação IoT pública.
* [Recursos de desenvolvimento](developer-reference-resource.md) para o LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

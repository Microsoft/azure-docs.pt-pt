---
title: O que é o Language Understanding (LUIS)?
description: Language Understanding (LUIS) - um serviço de API baseado na nuvem que utiliza machine-learning para linguagem conversacional e natural para prever significado e extrair informações.
keywords: Azure, inteligência artificial, ai, processamento de linguagem natural, nlp, compreensão da linguagem natural, nlu, ai conversa, ai conversador, ai chatbot, chatbot maker, LUIS, nlp ai, luis ai, azure luis, compreensão da linguagem natural
ms.topic: overview
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 22fe99e1552a9612adfbc455d60852f1591a1a54
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752144"
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

## <a name="natural-language-understanding-nlu"></a>Compreensão da linguagem natural (NLU)

[A LUIS fornece inteligência artificial (IA)](artificial-intelligence.md) sob a forma de NLU, um subconjunto de processamento de linguagem natural (NLP).

A sua aplicação LUIS contém um modelo de linguagem natural específico do domínio. Pode iniciar as aplicações LUIS com um modelo de domínio pré-criado, criar o seu próprio modelo ou misturar partes de um domínio pré-criado com as suas informações personalizadas.

* **Modelo pré-criado** O LUIS tem muitos modelos de domínio pré-criados, incluindo intenções, expressões e entidades pré-criadas. Pode utilizar as entidades pré-criadas sem utilizar as intenções e as expressões do modelo pré-criado. Os [modelos de domínio pré-criados](luis-how-to-use-prebuilt-domains.md) incluem todo o design para si e são uma excelente forma de começar a utilizar o LUIS rapidamente.

* **Modelo personalizado** A LUIS dá-lhe várias formas de identificar os seus próprios modelos personalizados, incluindo intenções e entidades. As entidades incluem entidades de aprendizagem automática, entidades específicas ou literais, e uma combinação de aprendizagem automática e literal.

Saiba mais sobre [o NLP](artificial-intelligence.md)e a área específica da LUIS da NLU.

## <a name="step-1-design-and-build-your-model"></a>Passo 1: Desenhe e construa o seu modelo

Desenhe o seu modelo com categorias de intenções de utilizador chamadas **[intenções](luis-concept-intent.md)**. Cada intenção precisa de exemplos de **[expressões](luis-concept-utterance.md)** do utilizador. Cada expressão pode fornecer dados que precisam de ser extraídos com [entidades de aprendizagem automática.](luis-concept-entity-types.md#effective-machine-learned-entities)

|Exemplo de expressão do utilizador|Intenção|Dados extraídos|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|ReservarBilhete|Seattle|
|`When does your store open?`|HoráriodaLojaeLocalização|abre|
|`Schedule a meeting at 1pm with Bob in Distribution`|AgendarReunião|13:00, João|

Construa o modelo com as APIs [de autoria,](https://go.microsoft.com/fwlink/?linkid=2092087) ou com o [**portal LUIS,**](https://www.luis.ai)ou ambos. Saiba mais como construir com o [portal](get-started-portal-build-app.md) e as [bibliotecas de clientes SDK.](quickstart-sdk.md)

## <a name="step-2-get-the-query-prediction"></a>Passo 2: Obter a previsão de consulta

Depois de o modelo da sua aplicação ser treinado e publicado no ponto final, uma aplicação do cliente (como um chat bot) envia declarações para a previsão [de](https://go.microsoft.com/fwlink/?linkid=2092356) API final. A API aplica o modelo à expressão para análise e responde com os resultados da previsão num formato JSON.

A resposta mínima do ponto final do JSON contém a expressão da consulta e a intenção com a melhor classificação. Também pode extrair dados como a seguinte entidade **do Tipo de Contacto** e o sentimento geral.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
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
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>Passo 3: Melhorar a previsão do modelo

Após a publicação da sua aplicação LUIS e receber declarações reais de utilizador, a LUIS fornece [uma aprendizagem ativa](luis-concept-review-endpoint-utterances.md) das expressões de ponto final para melhorar a precisão da previsão. Reveja estas sugestões como parte do seu trabalho regular de manutenção no seu ciclo de vida de desenvolvimento.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Ciclo de vida de desenvolvimento e ferramentas
A LUIS fornece ferramentas, versões e colaboração com outros autores luis para integrar em todo o ciclo de vida de [desenvolvimento.](luis-concept-app-iteration.md)

A Compreensão linguística (LUIS), como API REST, pode ser utilizada com qualquer produto, serviço ou enquadramento com um pedido HTTP. A LUIS também fornece bibliotecas de clientes (SDKs) para várias linguagens de programação de topo. Saiba mais sobre os [recursos de desenvolvimento](developer-reference-resource.md) fornecidos.

Ferramentas para utilizar o LUIS de forma rápida e fácil com um bot:
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) O pacote NPM fornece a autoria e previsão como uma ferramenta de linha de comando autónoma ou como importação.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen)– O LUISGen é uma ferramenta para gerar código fonte TypeScript e em C# com tipos de dados inflexíveis a partir de um modelo exportado do LUIS.
* O [Dispatch](https://aka.ms/dispatch-tool) permite utilizar uma variedade de aplicações do LUIS e de Criador de FAQ a partir de uma aplicação principal através da utilização do modelo de dispatcher.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) LUDown é uma ferramenta de linha de comando que ajuda a gerir modelos de linguagem para o seu bot.

## <a name="integrate-with-a-bot"></a>Integre-se com um bot

Utilize o [serviço Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) com o [Microsoft Bot Framework](https://dev.botframework.com/) para construir e implementar um chat bot. Desenhe e desenvolva com a ferramenta de interface gráfica, [Compositor,](https://docs.microsoft.com/composer/)ou [amostras de bot de trabalho projetadas](https://github.com/microsoft/BotBuilder-Samples) para cenários de bot de topo.

## <a name="integrate-with-other-cognitive-services"></a>Integrar-se com outros Serviços Cognitivos

Outros Serviços Cognitivos utilizados com o LUIS:
* [Criador de FAQ][qnamaker] - permite combinar vários tipos de textos numa base de conhecimento de perguntas e respostas.
* [Speech service](../Speech-Service/overview.md) - converte os pedidos de idioma falado em texto.

A LUIS fornece funcionalidade a partir de Text Analytics como parte dos seus recursos LUIS existentes. Esta funcionalidade inclui [a análise de sentimento](luis-how-to-publish-app.md#configuring-publish-settings) e a [extração de frases-chave](luis-reference-prebuilt-keyphrase.md) com a entidade keyPhrase pré-construída.

## <a name="learn-with-the-quickstarts"></a>Aprenda com os Quickstarts

Saiba mais sobre o LUIS com quickstarts práticos utilizando o [portal](get-started-portal-build-app.md) e as [bibliotecas de clientes SDK.](quickstart-sdk.md)


## <a name="next-steps"></a>Passos seguintes

* [Novidades](whats-new.md) com o serviço e documentação
* [Planeie a sua app](luis-how-plan-your-app.md) com [intenções](luis-concept-intent.md) e [entidades.](luis-concept-entity-types.md)
* [Consulta o ponto final da previsão.](luis-get-started-get-intent-from-browser.md)
* [Recursos de desenvolvimento](developer-reference-resource.md) para o LUIS.

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

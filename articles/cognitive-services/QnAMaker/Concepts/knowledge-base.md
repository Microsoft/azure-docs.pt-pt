---
title: Base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma base de dados de conhecimento do QnA Maker é composta por um conjunto de pares de pergunta/resposta (FAQ) e metadados opcionais associados a cada par de QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b3e74f337cf8f57321c3a8d94f8191fc3ebb530
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093905"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>O que é uma base QnA Maker para o conhecimento?

Uma base de dados de conhecimento do QnA Maker é composta por um conjunto de pares de pergunta/resposta (FAQ) e metadados opcionais associados a cada par de QnA.

## <a name="key-knowledge-base-concepts"></a>Conceitos chave base de dados de conhecimento

* **Perguntas** -uma pergunta contém o texto que melhor representa uma consulta de utilizador. 
* **Respostas** -uma resposta é a resposta retornada quando uma consulta de utilizador é correspondida com a pergunta associada.  
* **Metadados** -metadados são as marcas associadas com um par de QnA e são representados como pares chave-valor. As marcas de metadados são usadas para filtrar pares de QnA e limitar o conjunto sobre o qual a correspondência de consulta é executada.

Um único QnA, representada por um ID numérico do QnA, tem várias variantes de uma pergunta (perguntas alternativas) que todos mapeiam para uma única resposta. Além disso, cada par pode ter vários campos de metadados associados a ele: uma chave e um valor.

![Bases de dados de conhecimento do QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato de conteúdo de base de dados de conhecimento

Quando ingerir conteúdo avançado para uma base de dados de conhecimento, QnA Maker tenta converter o conteúdo em markdown. Leia [isso](https://aka.ms/qnamaker-docs-markdown-support) blogue para compreender o markdown formatos reconhecidos pela maioria dos clientes de bate-papo.

Campos de metadados são compostas por pares chave-valor, separados por vírgula **(produto: Shredder)** . Chave e valor tem de ser só de texto. A chave de metadados não pode conter quaisquer espaços. Os metadados dão suporte a apenas um valor por chave.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Como QnA Maker processa uma consulta de usuário para selecionar a melhor resposta

A base de dados de conhecimento treinada e [publicada](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker recebe uma consulta de usuário, de um bot ou outro aplicativo cliente, na [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). O diagrama a seguir ilustra o processo quando a consulta do usuário é recebida.

![O processo de classificação para uma consulta de usuário](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

O processo é explicado na tabela a seguir:

|Passo|Objetivo|
|--|--|
|1|O aplicativo cliente envia a consulta do usuário para a [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker pré-processando a consulta do usuário com detecção de idioma, grafias e separadores de palavras.|
|3|Esse pré-processamento é usado para alterar a consulta do usuário para obter melhores resultados de pesquisa.|
|4|Essa consulta alterada é enviada para Azure Search índice, recebendo o `top` número de resultados. Se a resposta correta não estiver nesses resultados, aumente o valor de `top` um pouco. Geralmente, um valor de 10 `top` para o funciona em 90% das consultas.|
|5|QnA Maker aplica o personalização Avançado para determinar a exatidão dos resultados de Azure Search buscados para a consulta do usuário. |
|6|O modelo de classificação treinado usa a Pontuação do recurso, da etapa 5, para classificar os resultados da Azure Search.|
|7|Os novos resultados são retornados ao aplicativo cliente em ordem classificada.|
|||

Os recursos usados incluem, mas não se limitam a semântica de nível de palavra, importância de nível de termo em um corpus e modelos semânticos aprendidos profundas para determinar a similaridade e a relevância entre duas cadeias de caracteres de texto.

## <a name="http-request-and-response-with-endpoint"></a>Solicitação e resposta HTTP com o ponto de extremidade
Quando você publica sua base de dados de conhecimento, o serviço cria um **ponto de extremidade** http baseado em REST que pode ser integrado ao seu aplicativo, normalmente um bot de bate-papo. 

### <a name="the-user-query-request-to-generate-an-answer"></a>A solicitação de consulta do usuário para gerar uma resposta

Uma **consulta de usuário** é a questão que o usuário final solicita na base de dados de conhecimento, `How do I add a collaborator to my app?`como,. A consulta geralmente está em um formato de linguagem natural ou algumas palavras-chave que representam a pergunta, como, `help with collaborators`. A consulta é enviada para seu conhecimento de uma **solicitação** http em seu aplicativo cliente.

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Você controla a resposta definindo propriedades como [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)e [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Use o [contexto de conversa](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) com a funcionalidade de [várias](../how-to/multiturn-conversation.md) instruções para manter a conversa a fim de refinar as perguntas e respostas, para encontrar a resposta correta e final.

### <a name="the-response-from-a-call-to-generate-answer"></a>A resposta de uma chamada para gerar resposta

A **resposta** http é a resposta recuperada da base de dados de conhecimento, com base na melhor correspondência para uma determinada consulta de usuário. A resposta inclui a resposta e a pontuação de previsão. Se você solicitou mais de uma resposta superior, com a `top` Propriedade, você obtém mais de uma resposta principal, cada uma com uma pontuação. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

### <a name="test-and-production-knowledge-base"></a>Base de dados de conhecimento de teste e produção
Uma base de dados de conhecimento é o repositório de perguntas e respostas criado, mantido e usado por meio do QnA Maker. Cada camada do QnA Maker pode ser utilizada para várias bases de dados de conhecimento.

Uma base de dados de conhecimento tem dois Estados - teste e publicado. 

A **base de dados de conhecimento de teste** é a versão que está sendo editada, salva e testada quanto à exatidão e à integridade das respostas. As alterações efetuadas para a base de dados de conhecimento de teste não afetam o utilizador final da sua aplicação/chatbot. A base de dados de conhecimento de `test` teste é conhecida como na solicitação HTTP. 

A **base de dados de conhecimento publicada** é a versão que é usada em seu aplicativo/bot de chat. A ação de publicação de uma base de dados de conhecimento coloca o conteúdo da base de dados de conhecimento de teste na versão publicada da base de dados de conhecimento. Uma vez que a base de dados de conhecimento publicado é a versão que o aplicativo utiliza através do ponto final, deve ter cuidado para garantir que o conteúdo está correto e bem testados. A base de dados de conhecimento publicada `prod` é conhecida como na solicitação HTTP. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ciclo de vida do desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

[Descrição geral do Criador de FAQ](../Overview/overview.md)

Crie e edite a base de dados de conhecimento com: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [SDK do .net](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Gerar resposta com: 
* [REST API](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [SDK do .net](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)

---
title: Base de dados de conhecimento-QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma base de dados de conhecimento QnA Maker consiste em um conjunto de pares de QnA (pergunta e resposta) e metadados opcionais associados a cada par de QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 355556e98300ecad6aa3141f0f4ab14b834cd91e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794889"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>O que é uma base de dados de conhecimento QnA Maker?

Uma base de dados de conhecimento QnA Maker consiste em um conjunto de pares de QnA (pergunta e resposta) e metadados opcionais associados a cada par de QnA.

## <a name="key-knowledge-base-concepts"></a>Principais conceitos da base de dados de conhecimento

* **Perguntas**: uma pergunta contém texto que melhor representa uma consulta de usuário. 
* **Respostas**: uma resposta é a resposta retornada quando uma consulta de usuário é correspondida com a pergunta associada.  
* **Metadados**: os metadados são marcas associadas a um par de QnA e são representados como pares de chave-valor. As marcas de metadados são usadas para filtrar pares de QnA e limitar o conjunto sobre o qual a correspondência de consulta é executada.

Uma única QnA, representada por uma ID de QnA numérica, tem várias variantes de uma pergunta (perguntas alternativas) que todas são mapeadas para uma única resposta. Além disso, cada par pode ter vários campos de metadados associados a ele: uma chave e um valor.

![Bases de dados de conhecimento QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato de conteúdo da base de dados de conhecimento

Quando você ingeri conteúdo rico em uma base de dados de conhecimento, o QnA Maker tenta converter o conteúdo para redução. Leia [este blog](https://aka.ms/qnamaker-docs-markdown-support) para saber mais sobre os formatos de redução que são compreensíveis pela maioria dos clientes de bate-papo.

Os campos de metadados consistem em pares chave-valor separados por dois-pontos, como produto: Shredder. A chave e o valor devem ser somente texto. A chave de metadados não deve conter espaços. Os metadados dão suporte a apenas um valor por chave.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Como QnA Maker processa uma consulta de usuário para selecionar a melhor resposta

A base de dados de conhecimento treinada e [publicada](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) QnA Maker recebe uma consulta de usuário, de um bot ou outro aplicativo cliente, na [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). O diagrama a seguir ilustra o processo quando a consulta do usuário é recebida.

![O processo de classificação para uma consulta de usuário](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Processo do classificador

O processo é explicado na tabela a seguir.

|Passo|Objetivo|
|--|--|
|1|O aplicativo cliente envia a consulta do usuário para a [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|QnA Maker pré-processa a consulta do usuário com detecção de idioma, grafias e separadores de palavras.|
|3|Esse pré-processamento é usado para alterar a consulta do usuário para obter os melhores resultados da pesquisa.|
|4|Essa consulta alterada é enviada para um índice de Pesquisa Cognitiva do Azure, que recebe o número `top` de resultados. Se a resposta correta não estiver nesses resultados, aumente o valor de `top` ligeiramente. Em geral, um valor de 10 para `top` funciona em 90% das consultas.|
|5|QnA Maker aplica o personalização Avançado para determinar a exatidão dos resultados da pesquisa buscada para a consulta do usuário. |
|6|O modelo de classificação treinado usa a Pontuação do recurso, da etapa 5, para classificar os resultados de Pesquisa Cognitiva do Azure.|
|7|Os novos resultados são retornados ao aplicativo cliente em ordem classificada.|
|||

Os recursos usados incluem, mas não são limitados a semântica de nível de palavra, importância de nível de termo em um corpus e modelos semânticos aprendidos profundos para determinar a similaridade e a relevância entre duas cadeias de caracteres de texto.

## <a name="http-request-and-response-with-endpoint"></a>Solicitação e resposta HTTP com o ponto de extremidade
Quando você publica sua base de dados de conhecimento, o serviço cria um ponto de extremidade HTTP baseado em REST que pode ser integrado ao seu aplicativo, normalmente um bot de bate-papo. 

### <a name="the-user-query-request-to-generate-an-answer"></a>A solicitação de consulta do usuário para gerar uma resposta

Uma consulta de usuário é a questão que o usuário final solicita na base de dados de conhecimento, como `How do I add a collaborator to my app?`. A consulta geralmente está em um formato de linguagem natural ou algumas palavras-chave que representam a pergunta, como `help with collaborators`. A consulta é enviada para sua base de dados de conhecimento de uma solicitação HTTP em seu aplicativo cliente.

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

### <a name="the-response-from-a-call-to-generate-an-answer"></a>A resposta de uma chamada para gerar uma resposta

A resposta HTTP é a resposta recuperada da base de dados de conhecimento, com base na melhor correspondência para uma determinada consulta de usuário. A resposta inclui a resposta e a pontuação de previsão. Se você solicitou mais de uma resposta principal com a propriedade `top`, obterá mais de uma resposta principal, cada uma com uma pontuação. 

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
Uma base de dados de conhecimento é o repositório de perguntas e respostas criadas, mantidas e usadas por meio de QnA Maker. Cada camada de QnA Maker pode ser usada para várias bases de dados de conhecimento.

Uma base de dados de conhecimento tem dois Estados: *teste* e *publicado*.

A *base de dados de conhecimento de teste* é a versão que está sendo editada, salva e testada quanto à exatidão e à integridade das respostas. As alterações feitas na base de dados de conhecimento de teste não afetam o usuário final do seu aplicativo ou bot de bate-papo. A base de dados de conhecimento de teste é conhecida como `test` na solicitação HTTP. 

A *base de dados de conhecimento publicada* é a versão que é usada no seu aplicativo ou bot de chat. A ação de publicar uma base de dados de conhecimento coloca o conteúdo da base de dados de conhecimento de teste na versão publicada da base de dados de conhecimento. Como a base de dados de conhecimento publicada é a versão que o aplicativo usa por meio do ponto de extremidade, verifique se o conteúdo está correto e bem testado. A base de dados de conhecimento publicada é conhecida como `prod` na solicitação HTTP.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ciclo de vida de desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consultar também

[Descrição geral do Criador de FAQ](../Overview/overview.md)

Criar e editar uma base de dados de conhecimento com: 
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Gerar uma resposta com: 
* [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)

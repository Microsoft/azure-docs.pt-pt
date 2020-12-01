---
title: Consulta da base de conhecimento - QnA Maker
description: Uma base de conhecimento deve ser publicada. Uma vez publicada, a base de conhecimento é consultada no ponto final de previsão de tempo de execução usando a API generateAnswer.
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: d8f986299edee46bf5cace7a9f4c805c29b3ce0c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346210"
---
# <a name="query-the-knowledge-base-for-answers"></a>Consultar a base de conhecimento para respostas

Uma base de conhecimento deve ser publicada. Uma vez publicada, a base de conhecimento é consultada no ponto final de previsão de tempo de execução usando a API generateAnswer. A consulta inclui o texto de perguntas, e outras definições, para ajudar o Criador QnA a selecionar a melhor correspondência possível para uma resposta.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Como o QnA Maker processa uma consulta do utilizador para selecionar a melhor resposta

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

A base de conhecimentos da QnA Maker treinada e [publicada](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) recebe uma consulta do utilizador, a partir de um bot ou outra aplicação de cliente, na [API GenerateAnswer](../how-to/metadata-generateanswer-usage.md). O diagrama seguinte ilustra o processo quando a consulta do utilizador é recebida.

![O processo de modelo de classificação para uma consulta de utilizador](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Processo ranker

O processo é explicado na tabela seguinte.

|Passo|Objetivo|
|--|--|
|1|A aplicação do cliente envia a consulta do utilizador para a [API GenerateAnswer](../how-to/metadata-generateanswer-usage.md).|
|2|O QnA Maker pré-processa a consulta do utilizador com deteção de idiomas, soletradores e disjuntores de palavras.|
|3|Este pré-processamento é tomado para alterar a consulta do utilizador para obter os melhores resultados de pesquisa.|
|4|Esta consulta alterada é enviada para um Índice de Pesquisa Cognitiva Azure, que recebe o `top` número de resultados. Se a resposta correta não estiver nestes resultados, aumente ligeiramente o `top` valor. Geralmente, um valor de 10 para `top` obras em 90% das consultas.|
|5|O QnA Maker utiliza uma caracterização baseada em sintática e semântica para determinar a semelhança entre a consulta do utilizador e os resultados do QnA.|
|6|O modelo de classificação aprendido pela máquina utiliza as diferentes funcionalidades, desde o passo 5, para determinar as pontuações de confiança e a nova ordem de classificação.|
|7|Os novos resultados são devolvidos ao pedido do cliente em ordem classificada.|
|||

As características usadas incluem, mas não se limitam à semântica de nível de palavras, importância de nível de termo em um corpus, e modelos semânticos profundos para determinar a semelhança e relevância entre duas cordas de texto.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

A base de conhecimentos da QnA Maker treinada e [publicada](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) recebe uma consulta do utilizador, a partir de um bot ou outra aplicação de cliente, na [API GenerateAnswer](../how-to/metadata-generateanswer-usage.md). O diagrama seguinte ilustra o processo quando a consulta do utilizador é recebida.

![O processo de modelo de classificação para uma pré-visualização de consulta de utilizador](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>Processo ranker

O processo é explicado na tabela seguinte.

|Passo|Objetivo|
|--|--|
|1|A aplicação do cliente envia a consulta do utilizador para a [API GenerateAnswer](../how-to/metadata-generateanswer-usage.md).|
|2|O QnA Maker pré-processa a consulta do utilizador com deteção de idiomas, soletradores e disjuntores de palavras.|
|3|Este pré-processamento é tomado para alterar a consulta do utilizador para obter os melhores resultados de pesquisa.|
|4|Esta consulta alterada é enviada para um Índice de Pesquisa Cognitiva Azure, que recebe o `top` número de resultados. Se a resposta correta não estiver nestes resultados, aumente ligeiramente o `top` valor. Geralmente, um valor de 10 para `top` obras em 90% das consultas.|
|5|O QnA Maker usa um modelo baseado em transformadores de última geração para determinar a semelhança entre a consulta do utilizador e os resultados do QnA candidatos recolhidos da Azure Cognitive Search. O modelo baseado em transformadores é um modelo multilingue de aprendizagem profunda, que funciona horizontalmente para todas as línguas para determinar as pontuações de confiança e a nova ordem de classificação.|
|6|Os novos resultados são devolvidos ao pedido do cliente em ordem classificada.|
|||

O ranker trabalha em todas as perguntas alternativas e resposta para encontrar os pares QnA mais compatíveis para a consulta do utilizador. Os utilizadores têm a flexibilidade para configurar o ranker para questionar apenas o ranker. 

---

## <a name="http-request-and-response-with-endpoint"></a>HTTP pedido e resposta com ponto final
Ao publicar a sua base de conhecimentos, o serviço cria um ponto final HTTP baseado em REST que pode ser integrado na sua aplicação, geralmente um chat bot.

### <a name="the-user-query-request-to-generate-an-answer"></a>O pedido de consulta do utilizador para gerar uma resposta

Uma consulta do utilizador é a pergunta que o utilizador final faz da base de conhecimentos, tais como `How do I add a collaborator to my app?` . A consulta é frequentemente num formato de linguagem natural ou em algumas palavras-chave que representam a questão, tais como `help with collaborators` . A consulta é enviada para a sua base de conhecimento a partir de um pedido HTTP na sua aplicação ao cliente.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Controla a resposta definindo propriedades como [scoreThreshold,](./confidence-score.md#choose-a-score-threshold) [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)e [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Utilize o contexto de [conversação](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) com [funcionalidades multi-voltas](../how-to/multiturn-conversation.md) para manter a conversa afinar as perguntas e respostas, para encontrar a resposta correta e final.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>A resposta de uma chamada para gerar uma resposta

A resposta HTTP é a resposta obtida a partir da base de conhecimento, com base na melhor correspondência para uma determinada consulta do utilizador. A resposta inclui a resposta e a pontuação de previsão. Se você pediu mais de uma resposta de topo com a `top` propriedade, você obtém mais do que uma resposta de topo, cada um com uma pontuação.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Pontuação de confiança](./confidence-score.md)
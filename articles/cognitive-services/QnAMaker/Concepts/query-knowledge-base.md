---
title: Consulta da base de conhecimento - QnA Maker
description: Uma base de conhecimento deve ser publicada. Uma vez publicada, a base de conhecimento é consultada no ponto final da previsão do tempo de execução utilizando a API geradaAnswer.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb777aa16fada50811cce1bbf49f28662c62b49b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389245"
---
# <a name="query-the-knowledge-base-for-answers"></a>Consulta da base de conhecimento para respostas

Uma base de conhecimento deve ser publicada. Uma vez publicada, a base de conhecimento é consultada no ponto final da previsão do tempo de execução utilizando a API geradaAnswer. A consulta inclui o texto de pergunta, e outras configurações, para ajudar o Fabricante QnA a selecionar o melhor fósforo possível para uma resposta.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Como o QnA Maker processa uma consulta de utilizador para selecionar a melhor resposta

A base de conhecimento da QnA Maker treinada e [publicada](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) recebe uma consulta de utilizador, de um bot ou outra aplicação de cliente, na [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). O diagrama seguinte ilustra o processo quando a consulta do utilizador é recebida.

![O processo de modelo de classificação para uma consulta de utilizador](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

### <a name="ranker-process"></a>Processo ranker

O processo é explicado na tabela seguinte.

|Passo|Objetivo|
|--|--|
|1|A aplicação do cliente envia a consulta do utilizador para a [API GenerateAnswer](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|O QnA Maker pré-processa a consulta do utilizador com deteção de idiomas, soletradores e quebra-palavras.|
|3|Este pré-processamento é tomado para alterar a consulta do utilizador para obter os melhores resultados de pesquisa.|
|4|Esta consulta alterada é enviada para um Índice de Pesquisa Cognitiva Azure, que recebe o número `top` de resultados. Se a resposta correta não estiver nestes resultados, aumente ligeiramente o valor da `top`. Geralmente, um valor de 10 para `top` trabalha em 90% das consultas.|
|5|QnA Maker usa característica sintática e semântica baseada para determinar a semelhança entre a consulta do utilizador e os resultados de QnA rebuscados.|
|6|O modelo de classificação aprendido com máquinas utiliza as diferentes características, desde o passo 5, para determinar as pontuações de confiança e a nova ordem de classificação.|
|7|Os novos resultados são devolvidos à aplicação do cliente por ordem classificada.|
|||

As funcionalidades utilizadas incluem, mas não se limitam à semântica de nível de palavra, importância de nível de termo num corpus, e modelos semânticos profundos e aprendidos para determinar a semelhança e a relevância entre duas cordas de texto.

## <a name="http-request-and-response-with-endpoint"></a>PEDIDO http e resposta com ponto final
Ao publicar a sua base de conhecimentos, o serviço cria um ponto final HTTP baseado em REST que pode ser integrado na sua aplicação, geralmente um chat bot.

### <a name="the-user-query-request-to-generate-an-answer"></a>O pedido de consulta do utilizador para gerar uma resposta

Uma consulta do utilizador é a questão que o utilizador final faz da base de conhecimento, como `How do I add a collaborator to my app?`. A consulta é frequentemente num formato de linguagem natural ou em algumas palavras-chave que representam a questão, como `help with collaborators`. A consulta é enviada para a sua base de conhecimento a partir de um pedido http no seu pedido de cliente.

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

Controla a resposta definindo propriedades como [pontuaçãoThreshold,](./confidence-score.md#choose-a-score-threshold) [top,](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)e [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags).

Use o contexto de [conversação](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) com [a funcionalidade multi-turn](../how-to/multiturn-conversation.md) para manter a conversa indo refinar as perguntas e respostas, para encontrar a resposta correta e final.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>A resposta de uma chamada para gerar uma resposta

A resposta HTTP é a resposta obtida da base de conhecimento, com base na melhor correspondência para uma determinada consulta de utilizador. A resposta inclui a resposta e a pontuação de previsão. Se você pediu mais do que uma resposta de topo com a propriedade `top`, você obtém mais do que uma resposta de topo, cada um com uma pontuação.

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

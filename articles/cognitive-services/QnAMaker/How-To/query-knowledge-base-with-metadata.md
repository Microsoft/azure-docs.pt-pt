---
title: Filtrar contextualização utilizando metadados
titleSuffix: Azure Cognitive Services
description: O QnA Maker filtra os pares QnA por metadados.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103022142"
---
# <a name="filter-responses-with-metadata"></a>Respostas de filtro com metadados

O QnA Maker permite-lhe adicionar metadados, sob a forma de pares de chaves e valor, aos seus pares de perguntas e respostas. Em seguida, pode utilizar estas informações para filtrar resultados para consultas do utilizador e para armazenar informações adicionais que podem ser usadas em conversas de seguimento.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Armazenar perguntas e respostas com uma entidade QnA

É importante entender como a QnA Maker armazena os dados de perguntas e respostas. A seguinte ilustração mostra uma entidade QnA:

![Ilustração de uma entidade QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidade QnA tem um ID único e persistente. Pode utilizar o ID para fazer atualizações a uma determinada entidade QnA.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Utilize metadados para filtrar respostas por etiquetas de metadados personalizados

A adição de metadados permite filtrar as respostas através destas etiquetas de metadados. Adicione a coluna de metadados no menu **Ver Opções.** Adicione metadados à sua base de conhecimento selecionando o ícone de metadados **+** para adicionar um par de metadados. Este par é composto por uma chave e um valor.

![Screenshot da adição de metadados](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar resultados com filtros rigorosos para tags de metadados

Considere a pergunta do utilizador "Quando é que este hotel fecha?", onde a intenção está implícita para o restaurante "Paradise".

Como os resultados são necessários apenas para o restaurante "Paradise", pode definir um filtro na chamada GenerateAnswer nos metadados "Nome do Restaurante". O exemplo a seguir mostra o seguinte:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Lógica E por defeito

Para combinar vários filtros de metadados na consulta, adicione os filtros de metadados adicionais à matriz da `strictFilters` propriedade. Por predefinição, os valores são logicamente combinados (E). Uma combinação lógica requer todos os filtros para combinar com os pares QnA para que o par seja devolvido na resposta.

Isto equivale a usar o `strictFiltersCompoundOperationType` imóvel com o valor de `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Lógico OU usando propriedade rígida deFiltersCompoundOperationType

Ao combinar vários filtros de metadados, se estiver apenas preocupado com um ou alguns dos filtros correspondentes, utilize a `strictFiltersCompoundOperationType` propriedade com o valor de `OR` .

Isto permite que a sua base de conhecimento devolva respostas quando qualquer filtro corresponde, mas não devolve respostas que não têm metadados.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Exemplos de metadados em arranques rápidos

Saiba mais sobre metadados no portal QnA Maker quickstart para metadados:
* [Autoria - adicione metadados ao par QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Previsão de consulta - respostas de filtro por metadados](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Use os resultados da pergunta e resposta para manter o contexto da conversação

A resposta ao GenerateAnswer contém as informações correspondentes de metadados do par de perguntas e respostas correspondentes. Pode utilizar esta informação na aplicação do seu cliente para armazenar o contexto da conversa anterior para utilização em conversas posteriores.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise a sua base de conhecimentos](../How-to/get-analytics-knowledge-base.md)

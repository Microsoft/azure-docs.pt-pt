---
title: 'Tutorial: listar entidade-LUIS'
titleSuffix: Azure Cognitive Services
description: Obtenha dados que correspondam a uma lista de itens predefinida. Cada item na lista pode ter sinónimos também com correspondência exata
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 056c64657f42d56879928f518598206d45493f60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447774"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Tutorial: obter dados de correspondência de texto exatos de um expressão com uma entidade de lista

Neste tutorial, entenda como obter dados que correspondem exatamente a uma lista predefinida de itens.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar aplicativo e usar a tentativa existente
> * Adicionar entidade de lista
> * Treinar, publicar e consultar o aplicativo para obter dados extraídos

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>O que é uma entidade de lista?

Uma entidade de lista é uma correspondência exata de texto com as palavras no expressão. Cada item na lista pode incluir uma lista de sinónimos. Use uma entidade de lista quando desejar uma correspondência exata.

Para esse aplicativo de pizza importado, crie uma entidade de lista para os diferentes tipos de pizza crust.

Uma entidade de lista é uma boa opção para este tipo de dados quando:

* Os valores dos dados são um conjunto conhecido.
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para este tipo de entidade.
* O texto na expressão é uma correspondência exata com um sinónimo ou o nome canónico. LUIS não usa a lista além de correspondências de texto exatas. A lematização, plurals e outras variações não são resolvidas com apenas uma entidade de lista. Para gerenciar variações, considere usar um [padrão](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

> [!CAUTION]
> Se você não tiver certeza se deseja uma entidade de lista ou uma entidade aprendida por computador com uma lista de frases como um descritor, a prática recomendada e mais flexível é usar uma entidade aprendida por máquina com uma lista de frases como um descritor. Esse método permite que o LUIS Aprenda e estenda os valores dos dados a serem extraídos.

## <a name="import-example-json-and-add-utterances"></a>Importar example. JSON e adicionar declarações

1.  Baixe e salve o [arquivo JSON do aplicativo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. O aplicativo importado tem uma intenção de `OrderPizza`. Selecione essa intenção e adicione alguns declarações com os novos tipos crust:

    |Novo declarações|
    |--|--|
    |peça uma pizza de Pan crust pequena pepperoni|
    |3 pizzas Crust do Havaí fino|
    |entregar pizzas de 2 inserida crust com pentes de pão|
    |uma pizza crustda espessa para retirada|
    |uma pizza Dish pepperoni em profundidade|

## <a name="crust-list-entity"></a>Entidade de lista de crust

Agora que a intenção **OrderPizza** tem um declarações de exemplo com tipos Crust, Luis precisa entender quais palavras representam os tipos crust.

Exemplos de nome primário e sinônimos são:

|Nome canônico|Sinónimos|
|--|--|
|Dish profunda|longo<br>crust Dish profunda<br>espessura<br>crust espesso|
|Deslocamento panorâmico|regularmente<br>Original<br>normal<br>crust regular<br>crust original<br>crust normal|
|Inserida|inserida crust|
|Dinâmico|crust fino<br>Skinn<br>Skinny crust|

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **+ criar**.

1. Na caixa de diálogo pop-up de entidade, introduza `CrustList` para o nome da entidade e **Lista** para o tipo de entidade. Selecione **Seguinte**.

    > [!div class="mx-imgBorder"]
    > ![captura de tela de criação de nova entidade de diálogo pop-up](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Na página **criar uma lista de entidades** , insira os nomes canônicos e sinônimos para cada nome canônico e, em seguida, selecione **criar**.

    > [!div class="mx-imgBorder"]
    > ![captura de tela da adição de itens para listar a entidade](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Quando você adiciona uma entidade de lista a um aplicativo LUIS, não precisa [rotular](label-entity-example-utterance.md) o texto com a entidade de lista. Ele é aplicado a todos os declarações em todas as intenções.

## <a name="train-the-app-before-testing-or-publishing"></a>Treinar o aplicativo antes de testar ou publicar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar o aplicativo para consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter previsão de intenção e entidade do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Vá para o final da URL no endereço e insira o seguinte expressão:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    O último parâmetro querystring é `query`, a expressão **query**.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Os tipos de crust foram encontrados como correspondências exatas de texto e retornados na resposta JSON. Essas informações são usadas pelo aplicativo cliente para processar o pedido.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Listar](luis-concept-entity-types.md#list-entity) informações conceituais da entidade
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Conceito – entidades](luis-concept-entity-types.md)
* [Referência JSON de entidade de expressão regular](reference-entity-regular-expression.md?tabs=V3)
* [Como adicionar entidades para extrair dados](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Passos seguintes
Este tutorial adicionou o exemplo declarações e, em seguida, criou uma entidade de lista para extrair correspondências exatas de texto do declarações. Depois de preparar e publicar a aplicação, uma consulta ao ponto final identifica a intenção e devolve os dados extraídos.

> [!div class="nextstepaction"]
> [Adicionar uma entidade predefinida com uma função](tutorial-entity-roles.md)


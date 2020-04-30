---
title: 'Tutorial: Entidade da lista - LUIS'
description: Obtenha dados que correspondam a uma lista de itens predefinida. Cada item na lista pode ter sinónimos também com correspondência exata
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 9530719c43260751d64d7ccf446bc7941078d6e9
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101132"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Tutorial: Obtenha dados exatos de texto de uma expressão com entidade da lista

Neste tutorial, entenda como obter dados que correspondam exatamente a uma lista predefinida de itens.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App de importação e utilização de intenções existentes
> * Adicionar entidade de lista
> * Treinar, publicar e consultar app para obter dados extraídos

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>O que é uma entidade de lista?

Uma entidade da lista é uma correspondência exata do texto com as palavras na expressão. Cada item na lista pode incluir uma lista de sinónimos. Use uma entidade da lista quando quiser uma correspondência exata.

Para esta aplicação de pizza importada, crie uma entidade de lista para os diferentes tipos de crosta de pizza.

Uma entidade de lista é uma boa opção para este tipo de dados quando:

* Os valores dos dados são um conjunto conhecido.
* O conjunto não excede os [limites](luis-limits.md) máximos do LUIS para este tipo de entidade.
* O texto na expressão é um jogo insensível com um sinónimo ou o nome canónico. Luis não usa a lista para além da partida. As variações, os plurales e outras variações não são resolvidos apenas com uma entidade de lista. Para gerir variações, considere usar um [padrão](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

> [!CAUTION]
> Se não tem a certeza se quer uma entidade de lista ou uma entidade aprendida com uma lista de frases como descritor, a melhor e mais flexível prática é usar uma entidade aprendida com uma lista de frases como descritor. Este método permite que a LUIS aprenda e estenda os valores dos dados para extrair.

## <a name="import-example-json-and-add-utterances"></a>Importar exemplo .json e adicionar expressões

1.  Descarregue e guarde o [ficheiro JSON](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)da aplicação .

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. A aplicação importada tem uma `OrderPizza` intenção. Selecione essa intenção e adicione algumas expressões com novos tipos de crosta:

    |Novas proclamações|
    |--|--|
    |por favor, peça uma crosta de panela pizza pepperoni pequena|
    |3 pizzas havaianas de crosta fina|
    |entregar 2 pizzas de crosta recheadas com palitos de pão|
    |uma pizza de crosta grossa para pickup|
    |uma pizza pepperoni prato profundo|

## <a name="crust-list-entity"></a>Entidade da lista de crostas

Agora que a intenção **da OrderPizza** tem pronunciações exemplo com tipos de crosta, a LUIS precisa de perceber quais as palavras que representam os tipos de crosta.

Exemplos do nome primário e sinónimos são:

|Nome canónico|Sinónimos|
|--|--|
|Prato profundo|profundo<br>crosta de prato profundo<br>grosso<br>crosta grossa|
|Pan|regular<br>Original<br>normal<br>crosta regular<br>crosta original<br>crosta normal|
|Recheado|crosta recheada|
|Dinâmico|crosta fina<br>magro<br>crosta magra|

1. Selecione **Entidades** no painel esquerdo.

1. Selecione **+ Criar**.

1. Na caixa de diálogo pop-up de entidade, introduza `CrustList` para o nome da entidade e **Lista** para o tipo de entidade. Selecione **Seguinte**.

    > [!div class="mx-imgBorder"]
    > ![Captura de ecrã da caixa de diálogo criação da nova entidade](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Na página **Criar uma lista,** introduza os nomes canónicos e sinónimos para cada nome canónico e, em seguida, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar itens à entidade listada](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Quando adiciona uma entidade de lista a uma aplicação LUIS, não precisa de [rotular](label-entity-example-utterance.md) o texto com a entidade da lista. É aplicado a todas as expressões em todas as intenções.

## <a name="train-the-app-before-testing-or-publishing"></a>Treine a app antes de testar ou publicar

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publique a app para consultar a partir do ponto final

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obtenha intenção e previsão de entidade a partir do ponto final

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Vá até ao fim do URL no endereço e introduza a seguinte expressão:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    O último parâmetro de `query`corda é, a **consulta**de expressão.


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

    Os tipos de crosta foram encontrados como correspondências de texto exatas e devolvidos na resposta json. Esta informação é utilizada pela aplicação do cliente para processar a encomenda.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Informação](luis-concept-entity-types.md#list-entity) conceptual da entidade de lista
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)
* [Conceito - entidades](luis-concept-entity-types.md)
* [Referência jSON de entidade de expressão regular](reference-entity-regular-expression.md?tabs=V3)
* [Como adicionar entidades para extrair dados](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Passos seguintes
Este tutorial acrescentou declarações de exemplo, então criou uma entidade da lista para extrair correspondências de texto exatas das expressões. Depois de preparar e publicar a aplicação, uma consulta ao ponto final identifica a intenção e devolve os dados extraídos.

> [!div class="nextstepaction"]
> [Adicionar entidade pré-construída com um papel](tutorial-entity-roles.md)


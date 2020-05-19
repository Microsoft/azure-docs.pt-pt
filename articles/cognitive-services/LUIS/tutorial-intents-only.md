---
title: 'Tutorial: Prever intenções - LUIS'
description: Crie uma aplicação personalizada que preveja a intenção de um utilizador com base na expressão (texto) neste tutorial.
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: c76273d7c180928d25be70e0abd7abf26c90b44a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588964"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Tutorial: Construir uma app LUIS para determinar as intenções dos utilizadores

Neste tutorial, cria-se uma aplicação personalizada que prevê a intenção de um utilizador com base na expressão (texto).

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar uma nova aplicação
> * Criar intenções
> * Adicionar expressões de exemplo
> * Preparar a aplicação
> * Publicar aplicação
> * Obtenha previsão de intenção a partir do ponto final

## <a name="user-intentions-as-intents"></a>Intenções de utilizador como intenções

O objetivo da app é determinar a intenção do texto de linguagem natural e conversacional:

`I'd like to order a veggie pizza with a salad on the side.`

Estas intenções estão categorizadas em **Intenções**.

|Intenção|Objetivo|
|--|--|
|`ModifyOrder`|Determina o pedido de pizza do utilizador.|
|`Greeting`|Comece a conversa com bot.|
|`ConfirmOrder`|Confirme a encomenda da pizza.|
|`None`|Determine se o utilizador está a perguntar algo que a aplicação LUIS não foi concebida para responder. Esta intenção é fornecida como parte da criação de apps e não pode ser eliminada. |

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Criar uma nova intenção

Uma intenção é usada para classificar as palavras do utilizador com base na intenção do utilizador, determinada a partir do texto de linguagem natural.

Para classificar uma expressão, a intenção necessita de exemplos de declarações de utilizadores que devem ser classificadas com esta intenção.

1. Na secção **Construir,** na página **Intenções,** selecione **+ Criar** para criar uma nova intenção. Introduza o novo nome de `OrderPizza` intenção, em seguida, selecione **Done**.

    A `OrderPizza` intenção é prevista quando um utilizador quer pedir uma pizza.

1. Adicione várias declarações de exemplo a esta intenção que espera que um utilizador pergunte:

    |`OrderPizza`expressões exemplo|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar pronunciações de exemplo no portal LUIS na página Intent](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Ao dar _exemplos,_ está a ensinar ao LUIS que tipo de declarações devem ser previstas para esta intenção. Estes são exemplos positivos. As declarações em todas as outras intenções são tratadas como exemplos negativos para esta intenção.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Criar intenções restantes

1. Crie a `Greeting` intenção e adicione as seguintes expressões exemplo. Esta é a intenção de determinar se um utilizador está a iniciar uma nova conversa de encomenda de pizza.

    |`Greeting`expressões exemplo|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Crie a `Confirm` intenção e adicione as seguintes expressões exemplo. Esta é a intenção de determinar se um utilizador é feito encomendando e aceita os detalhes da encomenda.

    |`Confirm`expressões exemplo|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Nenhuma intenção exemplo pronunciamentos

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publicar a aplicação

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Obtenha previsão de intenção

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até ao fim do URL na barra de endereços e introduza:

    `get a medium vegetarian pizza for delivery`

    Isto não é exatamente o mesmo que um exemplo de expressão, por isso é um bom teste para ver se LUIS pode aprender o que deve ser previsto com esta intenção.

    O último parâmetro de cadeia de consulta é `query`, a expressão **query**. Esta expressão não é igual a nenhuma das expressões de exemplo. É um bom teste e deverá devolver a intenção `OrderPizza` como a intenção com a melhor classificação.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    A matriz de entidades está vazia porque esta app atualmente não tem nenhuma entidade (unidade de dados dentro da expressão para extrair).

    O resultado da JSON identifica a principal intenção de pontuação como **`prediction.topIntent`** propriedade. Todos os resultados estão entre 1 e 0, com a melhor pontuação a estar mais perto de 1.

1. Altere o parâmetro de **consulta** URL para direcionar a intenção **de Saudação:**

    `Howdy`

    Isto não é exatamente o mesmo que um exemplo de expressão, por isso é um bom teste para ver se LUIS pode aprender o que deve ser previsto com esta intenção.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Esta previsão tem uma pontuação de confiança de 44%. Para aumentar a pontuação de confiança, adicione entre 15 e 30 expressões exemplo.

## <a name="client-application-next-steps"></a>Próximos passos da aplicação do cliente

Este tutorial criou uma app LUIS, criou intenções, acrescentou declarações de exemplo a cada intenção, acrescentou declarações de exemplo à intenção none, treinada, publicada e testada no ponto final. Estes são os passos básicos de criação de um modelo do LUIS.

Depois de devolver a resposta JSON, o LUIS conclui este pedido. O LUIS não fornece respostas às expressões do utilizador, apenas identifica o tipo de informação que está a ser pedida em linguagem natural. O acompanhamento de conversação é fornecido pela aplicação do cliente, como um Bot Azure.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tipos de entidades](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione uma entidade descompável a esta aplicação](tutorial-machine-learned-entity.md)

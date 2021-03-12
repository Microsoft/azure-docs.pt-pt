---
title: 'Tutorial: Prever intenções - LUIS'
description: Crie uma aplicação personalizada que preveja a intenção de um utilizador com base na expressão (texto) neste tutorial.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 04ba7dc79deef2dbad12c3f65a324201d3e9f598
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612767"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Tutorial: Construa uma app LUIS para determinar as intenções dos utilizadores

Neste tutorial, cria-se uma aplicação personalizada que prevê a intenção de um utilizador com base na expressão (texto).

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Criar uma nova aplicação
> * Criar intenções
> * Adicionar expressões de exemplo
> * Preparar a aplicação
> * Publicar aplicação
> * Obtenha previsão de intenção a partir do ponto final

## <a name="user-intentions-as-intents"></a>Intenções dos utilizadores como intenções

O objetivo da app é determinar a intenção de texto de linguagem natural e conversacional:

`I'd like to order a veggie pizza with a salad on the side.`

Estas intenções estão categorizadas em **Intenções**.

|Intenção|Objetivo|
|--|--|
|`ModifyOrder`|Determine o pedido de pizza do utilizador.|
|`Greeting`|Comece a conversa bot.|
|`ConfirmOrder`|Confirme o pedido de pizza.|
|`None`|Determine se o utilizador está a perguntar algo que a aplicação LUIS não foi concebida para responder. Esta intenção é fornecida como parte da criação de aplicações e não pode ser eliminada. |

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Criar uma nova intenção

Uma intenção é usada para classificar as expressões do utilizador com base na intenção do utilizador, determinada a partir do texto de linguagem natural.

Para classificar uma expressão, a intenção precisa de exemplos de declarações de utilizadores que devem ser classificadas com esta intenção.

1. Na secção **Construção,** na página **Intenções,** selecione **+ Criar** para criar uma nova intenção. Introduza o novo nome de `OrderPizza` intenção, e depois selecione **'Fazer' ( "Fazer")**

    A `OrderPizza` intenção é prevista quando um utilizador quer pedir uma pizza.

1. Adicione vários exemplos a esta intenção que espera que um utilizador pergunte:

    |`OrderPizza` palavras exemplo|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar palavras de exemplo no portal LUIS na página Intent](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Ao dar _palavras de exemplo,_ está a ensinar ao LUIS que tipo de proclamações devem ser previstas para esta intenção. Estes são exemplos positivos. As declarações em todas as outras intenções são tratadas como exemplos negativos para esta intenção.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Criar intenções restantes

1. Crie a `Greeting` intenção e adicione as seguintes palavras de exemplo. Esta é a intenção de determinar se um utilizador está a iniciar uma nova conversa de encomenda de pizza.

    |`Greeting` palavras exemplo|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Crie a `Confirm` intenção e adicione as seguintes palavras de exemplo. Esta é a intenção de determinar se um utilizador é feito encomendando e aceita os detalhes da encomenda.

    |`Confirm` palavras exemplo|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Nenhuma intenção exemplo declarações

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publicar a aplicação

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Obtenha previsão de intenção

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até o final do URL na barra de endereços e insira:

    `get a medium vegetarian pizza for delivery`

  
    O parâmetro da cadeia de consulta é `query` , a **consulta** de expressão é passada no URI. Esta expressão não é igual a nenhuma das expressões de exemplo. Este deve ser um bom teste para verificar se LUIS aprende e prevê a `OrderPizza` intenção como a intenção de pontuação superior.

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

    O conjunto de entidades está vazio porque esta app não tem atualmente nenhuma entidade (unidade de dados dentro da expressão para extrair).

    O resultado do JSON identifica a intenção de pontuação superior como **`prediction.topIntent`** propriedade. Todas as pontuações estão entre 1 e 0, com a melhor pontuação a ser mais próxima de 1.

1. Altere o parâmetro **de consulta** URL para direcionar a intenção **de Saudação:**

    `Howdy`

    Isto não é exatamente o mesmo que uma expressão de exemplo, por isso é um bom teste para ver se LUIS pode aprender o que deve ser previsto com esta intenção.

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

    Esta previsão tem uma pontuação de confiança de 44%. Para aumentar a pontuação de confiança, adicione entre 15 e 30 palavras de exemplo.

## <a name="client-application-next-steps"></a>Próximos passos da aplicação do cliente

Este tutorial criou uma app LUIS, criou intenções, acrescentou palavras de exemplo a cada intenção, acrescentou palavras de exemplo à intenção de Ninguém, treinada, publicada e testada no ponto final. Estes são os passos básicos de criação de um modelo do LUIS.

Depois de devolver a resposta JSON, o LUIS conclui este pedido. O LUIS não fornece respostas às expressões do utilizador, apenas identifica o tipo de informação que está a ser pedida em linguagem natural. O acompanhamento da conversação é fornecido pela aplicação do cliente, como um Bot Azure.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tipos de entidades](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)
* [Azure Bot](/azure/bot-service/)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione uma entidade decomposição a esta aplicação](tutorial-machine-learned-entity.md)
---
title: 'Tutorial: prever intenções-LUIS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, crie um aplicativo personalizado que prevê a intenção de um usuário. Esta aplicação é o tipo mais simples de aplicação LUIS, porque não extrai vários elementos de dados do texto da expressão, como endereços de e-mail ou datas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 89fb76f8c5cc4323e1211524340c0965a7d0716d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262759"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Tutorial: criar um aplicativo LUIS para determinar as intenções do usuário

Neste tutorial, você cria um aplicativo personalizado que prevê a intenção de um usuário com base no expressão (texto).

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * Criar uma nova aplicação
> * Criar intenções
> * Adicionar expressões de exemplo
> * Preparar a aplicação
> * Publicar aplicação
> * Obter previsão de intenção do ponto de extremidade

## <a name="user-intentions-as-intents"></a>Intenções do usuário como tentativas

A finalidade do aplicativo é determinar a intenção do texto de idioma natural, de conversação:

`I'd like to order a veggie pizza with a salad on the side.`

Estas intenções estão categorizadas em **Intenções**.

|Intenção|Finalidade|
|--|--|
|`ModifyOrder`|Determinar a ordem de pizza do usuário.|
|`Greeting`|Iniciar conversa de bot.|
|`ConfirmOrder`|Confirme a ordem de pizza.|
|`None`|Determine se o usuário está solicitando algo que o aplicativo não deve responder. Essa tentativa se fornecida como parte da criação do aplicativo e não pode ser excluída. |

## <a name="create-a-new-app"></a>Criar uma nova aplicação

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Criar uma nova intenção

1. No portal, dentro da seção **Build** do aplicativo, selecione **+ criar**. Insira o nome da nova tentativa, `OrderPizza`e, em seguida, selecione **concluído**.

    A intenção de `OrderPizza` é prevista quando: um usuário deseja solicitar uma pizza.

1. Adicione vários exemplos de declarações a essa intenção que você espera que um usuário pergunte:

    |`OrderPizza` exemplo declarações|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    ![Adicionar expressões de exemplo](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Ao fornecer o _exemplo declarações_, você está treinando Luis sobre quais tipos de declarações devem ser previstos para essa intenção.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Criar tentativas restantes

1. Crie a tentativa de `Greeting` e adicione o exemplo a seguir declarações. Essa é a intenção de determinar se um usuário está começando uma conversa de ordem de pizza nova.

    |`Greeting` exemplo declarações|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Crie a tentativa de `Confirm` e adicione o exemplo a seguir declarações. Essa é a intenção de determinar se um usuário é solicitado e aceita os detalhes do pedido.

    |`Confirm` exemplo declarações|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Exemplo de intenção de nenhum declarações

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publicar a aplicação

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Obter previsão de intenção

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL na barra de endereços e digite:

    `get a medium vegetarian pizza for delivery`

    Isso não é exatamente o mesmo que um exemplo de expressão, portanto, é um bom teste para ver se o LUIS pode aprender o que deve ser previsto com essa intenção.

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

    A matriz de entidades está vazia porque esse aplicativo atualmente não tem nenhuma entidade (unidade de dados dentro do expressão a ser extraído).

    O resultado JSON identifica a intenção com a melhor classificação como a propriedade **`prediction.topIntent`** . Todas as pontuações estão entre 1 e 0, com a melhor pontuação sendo mais próxima de 1.

1. Altere o parâmetro de **consulta** de URL para direcionar a intenção de **saudação** :

    `Howdy`

    Isso não é exatamente o mesmo que um exemplo de expressão, portanto, é um bom teste para ver se o LUIS pode aprender o que deve ser previsto com essa intenção.

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

    Essa previsão tem uma pontuação de confiança de 44%. Para aumentar a pontuação de confiança, adicione entre 15 e 30 exemplos de declarações.

## <a name="client-application-next-steps"></a>Próximas etapas do aplicativo cliente

Depois de devolver a resposta JSON, o LUIS conclui este pedido. O LUIS não fornece respostas às expressões do utilizador, apenas identifica o tipo de informação que está a ser pedida em linguagem natural. O acompanhamento de conversação é fornecido pelo aplicativo cliente, como um bot do Azure.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tipos de entidades](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Passos seguintes

Este tutorial criou um aplicativo LUIS, criou tentativas, adicionou um exemplo de declarações a cada tentativa, adicionou um exemplo declarações à intenção de nenhum, treinado, publicado e testado no ponto de extremidade. Estes são os passos básicos de criação de um modelo do LUIS.

> [!div class="nextstepaction"]
> [Adicionar uma entidade combinável a este aplicativo](tutorial-machine-learned-entity.md)

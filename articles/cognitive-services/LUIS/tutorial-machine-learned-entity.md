---
title: 'Tutorial: extrair dados estruturados com entidade aprendida por máquinas - LUIS'
titleSuffix: Azure Cognitive Services
description: Extrair dados estruturados de uma expressão utilizando a entidade aprendida pela máquina. Para aumentar a precisão de extração, adicione subcomponentes com descritores e restrições.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: e1709a5e86c8fed8d7f724ad1b105bd02df9fa56
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75381771"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Tutorial: Extrair dados estruturados a partir da expressão do utilizador com entidades aprendizes de máquinas em Compreensão linguística (LUIS)

Neste tutorial, extrair dados estruturados a partir de uma expressão utilizando a entidade aprendida pela máquina.

A entidade aprendida por máquinas suporta o conceito de [decomposição](luis-concept-model.md#v3-authoring-model-decomposition) do modelo, fornecendo às entidades subcomponentes os seus descritores e constrangimentos.

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * App de exemplo de importação
> * Adicionar entidade aprendida por máquinas
> * Adicionar subcomponente
> * Adicione o descritor do subcomponente
> * Adicionar a restrição do subcomponente
> * Preparar a aplicação
> * App de teste
> * Publicar aplicação
> * Obtenha a previsão da entidade a partir do ponto final

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Por que usar uma entidade aprendida por máquinas?

Este tutorial adiciona uma entidade aprendida por máquinas para extrair dados de uma expressão.

O objetivo de uma entidade é definir os dados a extrair. Isto inclui dar aos dados um nome, um tipo (se possível), qualquer resolução dos dados se houver ambiguidade, e o texto exato que compõe os dados.

Para definir a entidade, é necessário criar a entidade e depois rotular o texto que representa a entidade na expressão do exemplo. Estes exemplos rotulados ensinam ao LUIS o que é a entidade e onde pode ser encontrada numa expressão.

## <a name="entity-decomposability-is-important"></a>A descomposição da entidade é importante

A descomposição da entidade é importante tanto para a previsão das intenções como para a extração de dados.

Comece com uma entidade aprendida por máquinas, que é a entidade de início e de alto nível para a extração de dados. Em seguida, decomponha a entidade nas peças necessárias pela aplicação do cliente.

Embora não saiba o quão detalhado pretende a sua entidade quando iniciar a sua aplicação, a melhor prática é começar com uma entidade aprendida com máquinas e depois decompor-se com subcomponentes à medida que a sua aplicação amadurece.

Em termos práticos, irá criar uma entidade aprendida com máquinas para representar uma encomenda de uma aplicação de pizza. A ordem deve ter todas as peças necessárias para completar a encomenda. Para começar, a entidade irá extrair texto relacionado com a ordem, retirando o tamanho e a quantidade.

Uma expressão `Please deliver one large cheese pizza to me` para `one large cheese pizza` deve extrair como ordem, em seguida, também extrair `1` e `large`.

Há mais decomposição que pode adicionar, como criar subcomponentes para coberturas ou crostas. Após este tutorial, deve sentir-se confiante em `Order` adicionar estes subcomponentes à sua entidade existente.

## <a name="import-example-json-to-begin-app"></a>Exemplo de importação .json para iniciar app

1.  Descarregue e guarde o [ficheiro JSON](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)da aplicação .

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Rotular texto como entidades em exemplo declarações

Para extrair detalhes sobre uma encomenda de pizza, `Order` crie uma entidade de alto nível, aprendida por máquinas.

1. Na página **Intenções,** selecione a intenção **OrderPizza.**

1. Na lista de pronunciamentos de exemplo, selecione a seguinte expressão.

    |Pronunciamento de exemplo de ordem|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Comece a selecionar pouco antes do `pickup` texto mais à esquerda de (#1), em seguida, ir além do texto mais à direita , `anchovies` (#2 - isto termina o processo de rotulagem). Aparece um menu pop-up. Na caixa pop-up, insira o `Order` nome da entidade como (#3). Em `Order - Create new entity` seguida, selecione a partir da lista (#4).

    ![Etiqueta de início e fim do texto para encomenda completa](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Uma entidade nem sempre será a expressão completa. Neste caso específico, `pickup` indica como a ordem deve ser recebida. Do ponto de `pickup` vista conceptual, deve fazer parte da entidade rotulada para a encomenda.

1. Na caixa de **tipo Desemletra uma entidade,** selecione **Adicionar Estrutura** e, em seguida, selecione **Next**. A estrutura é necessária para adicionar subcomponentes, como tamanho e quantidade.

    ![Adicionar estrutura à entidade](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Na caixa de **entidades aprendidas** por máquinas, na caixa **Estrutura,** adicione `Size` e, em seguida, selecione Enter.
1. Para adicionar um **descritor,** selecione o `+` **descritor para** tamanho e, em seguida, selecione Criar uma nova lista de **frases**.

1. Na caixa de **descritore sinuoso** da nova lista de frases, introduza o nome `SizeDescriptor` e introduza valores de: `small`, `medium`e `large`. Quando **a** caixa sugestões preencher, selecione `extra large`, e `xl`. Selecione **Feito** para criar a nova lista de frases.

    Este descritor da lista `Size` de frases ajuda o subcomponente a encontrar palavras relacionadas com o tamanho, fornecendo-lhe palavras de exemplo. Esta lista não precisa de incluir todas as palavras de tamanho, mas deve incluir palavras que se espera que indiquem tamanho.

    ![Criar um descritor para o subcomponente de tamanho](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Na janela **criar uma entidade aprendida** por `Size` máquina, selecione **Criar** para terminar a criação do subcomponente.

    A `Order` entidade `Size` com um componente `Order` é criada mas apenas a entidade foi aplicada à expressão. É necessário rotular `Size` o texto da entidade na expressão do exemplo.

1. No mesmo exemplo, rotule o subcomponente **tamanho** de, `large` selecionando a palavra e selecionando a entidade **Tamanho** da lista de abandono.

    ![Rotule a entidade de tamanho para texto na expressão.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    A linha é sólida sob o texto porque tanto a rotulagem como a previsão coincidem porque rotulou explicitamente o texto.

1. Rotule `Order` a entidade nas restantes declarações juntamente com a entidade de tamanho. Os suportes quadrados do texto `Order` indicam `Size` a entidade rotulada e a entidade no seu interior.

    |Palavras exemplo de ordem|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Faça entidade e subcomponentes em todas as declarações restantes de exemplo.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Como se tratam dados implícitos, como a letra, `a` implicando uma única pizza? Ou a `pickup` falta `delivery` e para indicar onde a pizza é esperada? Ou a falta de tamanho para indicar o seu tamanho padrão de pequeno ou grande? Considere tratar o tratamento implícito de dados como parte das suas regras de negócio na aplicação do cliente em vez de ou além de LUIS.

1. Para treinar a aplicação, selecione **Train**. A formação aplica as alterações, como as novas entidades e as expressões rotuladas, ao modelo ativo.

1. Após o treino, adicione um novo exemplo de expressão à intenção de ver como luis entende bem a entidade aprendida pela máquina.

    |Pronunciamento de exemplo de ordem|
    |--|
    |`pickup XL meat lovers pizza`|

    A entidade de `Order` topo global, `Size` está rotulada e o subcomponente também está rotulado com linhas pontilhadas. Esta é uma previsão bem sucedida.

    ![Nova expressão de exemplo prevista com entidade](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A linha pontilhada indica a previsão.

1. Para alterar a previsão numa entidade rotulada, selecione a linha e, em seguida, selecione **confirmar previsões da entidade**.

    ![Aceite a previsão selecionando a previsão da entidade confirmar.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Neste momento, a entidade aprendida por máquinas está a trabalhar porque pode encontrar a entidade dentro de um novo exemplo. À medida que acrescenta súmulas exemplo, se a entidade não for prevista corretamente, rotule a entidade e os subcomponentes. Se a entidade estiver corretamente prevista, certifique-se de confirmar as previsões.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Adicione número pré-construído para ajudar a extrair dados

A informação da encomenda também deve incluir quantas de um item está na encomenda, como quantas pizzas. Para extrair estes dados, é necessário adicionar um `Order` novo subcomponente aprendido com máquinas e esse componente necessita de uma restrição de um número pré-construído. Ao limitar a entidade a um número pré-construído, a entidade irá encontrar `2`e extrair `two`números se o texto é um dígito, ou texto, .

Comece por adicionar a entidade de números pré-construída à app.

1. Selecione **Entidades** do menu esquerdo e, em seguida, selecione **+ Adicione entidade pré-construída**.

1. Na caixa **adicionar entidades pré-construídas,** procure e selecione **o número** e selecione **Done**.

    ![Adicionar entidade pré-construída](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    A entidade pré-construída é adicionada à app, mas ainda não é um constrangimento.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Criar entidade subcomponente com restrição para ajudar a extrair dados

A `Order` entidade deve `Quantity` ter um subcomponente para determinar quantos de um item estão na ordem. A quantidade deve ser limitada a um número para que os dados extraídos sejam imediatamente utilizáveis pela aplicação do cliente.

Uma restrição é aplicada como uma correspondência de texto, quer com correspondência exata (como uma entidade de lista) quer através de expressões regulares (como uma entidade de expressão regular ou uma entidade pré-construída).

Ao utilizar uma restrição, apenas o texto que corresponde a essa restrição é extraído.

1. Selecione **Entidades** e selecione a `Order` entidade.
1. Selecione **+ Adicionar** `Quantity` Componente e, em seguida, introduza o nome e selecione Entrar para adicionar a nova entidade à aplicação.
1. Após a notificação `Quantity` de sucesso, selecione o subcomponente e, em seguida, selecione o lápis 'Restrição'.
1. Na lista de lançamentos, selecione o número pré-construído.

    ![Criar entidade de quantidade com número pré-construído como restrição.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    A `Quantity` entidade é aplicada se e apenas se for encontrado texto correspondente à entidade número pré-construída.

    A entidade com o constrangimento é criada mas ainda não aplicada às declarações de exemplo.

    > [!NOTE]
    > Um subcomponente pode ser aninhado dentro de um subcomponente até 5 níveis. Embora isto não seja mostrado neste artigo, está disponível a partir do portal e da API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Rotule a expressão do exemplo para ensinar LUIS sobre a entidade

1. Selecione **Intenções** a partir da navegação à esquerda e, em seguida, selecione a intenção **OrderPizza.** Os três números nas seguintes expressões são rotulados, mas estão visualmente abaixo da linha da `Order` entidade. Este nível mais baixo significa que as entidades são encontradas, mas não são consideradas separadas da `Order` entidade.

    ![O número pré-construído é encontrado, mas ainda não considerado à parte da entidade da Ordem.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Marque os `Quantity` números com `2` a entidade selecionando `Quantity` a expressão no exemplo e selecionando a partir da lista. Rotule `6` a `1` e a expressão no mesmo exemplo.

    ![Rotular texto com entidade de quantidade.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Treine a app para aplicar as alterações da entidade na app

Selecione **Train** para treinar a app com estas novas expressões.

![Treine a aplicação e reveja as declarações de exemplo.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Neste ponto, a encomenda tem alguns detalhes que podem ser extraídos (tamanho, quantidade e texto total de encomenda). Há ainda refinação `Order` da entidade, como coberturas de pizza, tipo de crosta e encomendas laterais. Cada um deles deve ser criado `Order` como subcomponentes da entidade.

## <a name="test-the-app-to-validate-the-changes"></a>Teste a app para validar as alterações

Teste a aplicação utilizando o painel de **teste** interativo. Este processo permite-lhe entrar numa nova expressão e depois ver os resultados da previsão para ver como a aplicação ativa e treinada está a funcionar. A previsão das intenções deve ser bastante confiante (acima de 70%) e a extração da entidade `Order` deve recolher pelo menos a entidade. Os detalhes da entidade da ordem podem estar desaparecidos porque 5 expressões não são suficientes para lidar com todos os casos.

1. Selecione **Test** (Testar) no painel de navegação superior.
1. Introduza a `deliver a medium veggie pizza` expressão e selecione Enter. O modelo ativo previu a intenção correta com mais de 70% de confiança.

    ![Insira uma nova expressão para testar a intenção.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. **Selecione Inspecionar** para ver as previsões da entidade.

    ![Veja as previsões da entidade no painel de testes interativo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    O tamanho foi corretamente identificado. Lembre-se que as declarações de exemplo na `OrderPizza` `medium` intenção não têm um exemplo de `SizeDescriptor` tamanho, mas use um descritor de uma lista de frases que inclua meio.

    A quantidade não está corretamente prevista. Para corrigir isto, pode adicionar mais declarações de exemplo usando essa palavra `Quantity` para indicar quantidade e rotular essa palavra como uma entidade.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publique a app para aceder a ela a partir do ponto final http

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Obtenha intenção e previsão de entidade do ponto final do HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até ao final do URL no endereço e introduza a mesma consulta que inseriu no painel de teste interativo.

    `deliver a medium veggie pizza`

    O último parâmetro de `query`corda é, a **consulta**de expressão.

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
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


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial - intenções](luis-quickstart-intents-only.md)
* [Conceito - informação](luis-concept-entity-types.md) conceptual de entidades
* [Conceito - apresenta](luis-concept-feature.md) informação conceptual
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, a aplicação utiliza uma entidade aprendida por máquinas para encontrar a intenção da expressão de um utilizador e extrair detalhes dessa expressão. A utilização da entidade aprendida por máquinas permite-lhe decompor os detalhes da entidade.

> [!div class="nextstepaction"]
> [Adicionar uma entidade keyphrase criada previamente](luis-quickstart-intent-and-key-phrase.md)

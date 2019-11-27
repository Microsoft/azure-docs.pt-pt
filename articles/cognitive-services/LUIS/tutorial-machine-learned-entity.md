---
title: 'Tutorial: extrair dados estruturados com entidades aprendidas por máquina-LUIS'
titleSuffix: Azure Cognitive Services
description: Extrair dados estruturados de um expressão usando a entidade aprendida por máquina. Para aumentar a precisão de extração, adicione subcomponentes com descritores e restrições.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 913fa3c846ea00649a584be02975fdde449dc7cf
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383290"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Tutorial: extrair dados estruturados do expressão do usuário com entidades aprendidas pelo computador no Reconhecimento vocal (LUIS)

Neste tutorial, extraia dados estruturados de um expressão usando a entidade aprendida por computador. 

A entidade aprendida por máquina dá suporte ao [conceito de decomposição de modelo](luis-concept-model.md#v3-authoring-model-decomposition) fornecendo entidades de subcomponente com seus descritores e restrições. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Importar aplicativo de exemplo
> * Adicionar entidade aprendida pela máquina 
> * Adicionar subcomponente
> * Adicionar descritor do subcomponente
> * Adicionar restrição do subcomponente
> * Preparar a aplicação
> * Aplicativo de teste
> * Publicar aplicação
> * Obter previsão de entidade do ponto de extremidade

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Por que usar uma entidade aprendida por máquina?

Este tutorial adiciona uma entidade aprendida por computador para extrair dados de um expressão. 

A finalidade de uma entidade é definir os dados a serem extraídos. Isso inclui fornecer aos dados um nome, um tipo (se possível), qualquer resolução dos dados se houver ambiguidade e o texto exato que compõe os dados. 

Para definir a entidade, você precisa criar a entidade e, em seguida, rotular o texto que representa a entidade no exemplo expressão. Esses exemplos rotulados ensinam a LUIS o que é a entidade e onde ela pode ser encontrada em um expressão. 

## <a name="entity-decomposability-is-important"></a>A desdação da entidade é importante

A desdação da entidade é importante para a previsão de intenção e para extração de dados. 

Comece com uma entidade aprendida por computador, que é a entidade de início e de nível superior para extração de dados. Em seguida, decompor a entidade nas partes necessárias pelo aplicativo cliente. 

Embora você talvez não saiba como você deseja que sua entidade ao iniciar seu aplicativo, uma prática recomendada é começar com uma entidade aprendida por computador e, em seguida, decompor com subcomponentes à medida que seu aplicativo amadurece.

Em termos práticos, você criará uma entidade aprendida por computador para representar um pedido para um aplicativo de pizza. A ordem deve ter todas as partes necessárias para fullfil o pedido. Para começar, a entidade extrairá o texto relacionado à ordem, o tamanho de extração e a quantidade. 

Um expressão para `Please deliver one large cheese pizza to me` deve extrair `one large cheese pizza` como a ordem, e também extrair `1` e `large`. 

Há uma decomposição adicional que você pode adicionar, como a criação de subcomponentes para ingredientes ou crust. Após este tutorial, você deve sentir confiante para adicionar esses subcomponentes à sua entidade de `Order` existente.

## <a name="import-example-json-to-begin-app"></a>Importar example. JSON para iniciar o aplicativo

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

1. No [portal do Luis de visualização](https://preview.luis.ai), na página **meus aplicativos** , selecione **importar**e **importar como JSON**. Localize o arquivo JSON salvo da etapa anterior. Você não precisa alterar o nome do aplicativo. Selecionar **concluído**

1. Na seção **gerenciar** , na guia **versões** , selecione a versão, selecione **clonar** para clonar a versão e nomeie-a `mach-learn`e, em seguida, selecione **concluído** para concluir o processo de clonagem. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

    > [!TIP] 
    > A clonagem de uma versão em uma nova versão é uma prática recomendada antes de modificar seu aplicativo. Quando você concluir uma versão, exporte a versão (como um arquivo. JSON ou. Lu) e verifique o arquivo em seu sistema de controle do código-fonte.

1. Selecione **Compilar** e, em seguida, para ver **as intenções,** os principais blocos de construção de um aplicativo Luis.

    ![Altere da página versões para a página de tentativas.](media/tutorial-machine-learned-entity/new-version-imported-app.png)

## <a name="label-text-as-entities-in-example-utterances"></a>Rotular texto como entidades no exemplo declarações

Para extrair detalhes sobre uma ordem de pizza, crie uma entidade de `Order` de nível superior aprendida por computador.

1. Na página **tentativas** , selecione a intenção **OrderPizza** . 

1. Na lista declarações de exemplo, selecione o seguinte expressão. 

    |Expressão de exemplo de pedido|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Comece a selecionar pouco antes do texto mais à esquerda de `pickup` (#1) e, em seguida, vá além do texto mais à direita, `anchovies` (#2-isso encerra o processo de rotulagem). Um menu pop-up é exibido. Na caixa pop-up, digite o nome da entidade como `Order` (#3). Em seguida, selecione `Order - Create new entity` na lista (#4).

    ![Rótulo início e fim do texto para a ordem concluída](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Uma entidade nem sempre será a expressão inteira. Nesse caso específico, `pickup` indica como o pedido deve ser recebido. Do ponto de vista conceitual, `pickup` deve fazer parte da entidade rotulada para o pedido. 

1. Na caixa **escolher um tipo de entidade** , selecione **Adicionar estrutura** e selecione **Avançar**. A estrutura é necessária para adicionar subcomponentes, como tamanho e quantidade.

    ![Adicionar estrutura à entidade](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Na caixa **criar uma entidade aprendida pela máquina** , na caixa **estrutura** , adicione `Size`, em seguida, selecione Enter. 
1. Para adicionar um **descritor**, selecione a `+` na área **descritores para tamanho** e, em seguida, selecione **criar nova lista de frases**.

1. Na caixa **criar novo descritor de lista de frases** , digite o nome `SizeDescriptor` Insira os valores de: `small`, `medium`e `large`. Quando a caixa **sugestões** estiver preenchida, selecione `extra large`e `xl`. Selecione **concluído** para criar a nova lista de frases. 

    Esse descritor de lista de frases ajuda o subcomponente `Size` a localizar palavras relacionadas ao tamanho, fornecendo-a com palavras de exemplo. Essa lista não precisa incluir todas as palavras de tamanho, mas deve incluir palavras que são esperadas para indicar o tamanho. 

    ![Criar um descritor para o subcomponente de tamanho](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Na janela **criar uma entidade aprendida pela máquina** , selecione **criar** para concluir a criação do subcomponente `Size`.  

    A entidade `Order` com um componente `Size` é criada, mas apenas a entidade `Order` foi aplicada ao expressão. Você precisa rotular o texto da entidade `Size` no exemplo expressão. 

1. No mesmo exemplo expressão, Rotule o subcomponente de **tamanho** de `large` selecionando a palavra e, em seguida, selecionando a entidade **tamanho** na lista suspensa. 

    ![Rotule a entidade de tamanho para texto no expressão.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    A linha está sólida sob o texto porque a rotulagem e a previsão correspondem, pois você rotulava explicitamente o texto.

1. Rotule a entidade de `Order` no declarações restante junto com a entidade de tamanho. Os colchetes no texto indicam a entidade de `Order` rotulada e a entidade de `Size` no.

    |Declarações de exemplo de pedido|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Torne a entidade e os subcomponentes em todos os exemplos restantes de declarações.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Como tratar dados implícitos, como a letra `a` implicando em uma pizza única? Ou a falta de `pickup` e `delivery` para indicar onde a pizza é esperada? Ou a falta de um tamanho para indicar o tamanho padrão de pequeno ou grande? Considere tratar a manipulação de dados implícita como parte de suas regras de negócios no aplicativo cliente em vez de ou além de LUIS. 

1. Para treinar o aplicativo, selecione **treinar**. O treinamento aplica as alterações, como as novas entidades e rotuladas declarações, ao modelo ativo.

1. Após o treinamento, adicione um novo exemplo expressão à intenção de ver como a LUIS entende a entidade aprendida pela máquina. 

    |Expressão de exemplo de pedido|
    |--|
    |`pickup XL meat lovers pizza`|

    A entidade de cima geral, `Order` é rotulada e o subcomponente de `Size` também é rotulado com linhas pontilhadas. Esta é uma previsão bem-sucedida. 

    ![Novo exemplo expressão previsto com a entidade](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A linha pontilhada indica a previsão. 

1. Para alterar a previsão para uma entidade rotulada, selecione a linha e, em seguida, selecione **confirmar previsões de entidade**.

    ![Aceite a previsão selecionando confirmar previsão de entidade.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Neste ponto, a entidade aprendida por máquina está funcionando porque pode encontrar a entidade em um novo exemplo expressão. Ao adicionar o exemplo declarações, se a entidade não for prevista corretamente, rotule a entidade e os subcomponentes. Se a entidade for prevista corretamente, certifique-se de confirmar as previsões. 

## <a name="add-prebuilt-number-to-help-extract-data"></a>Adicionar número predefinido para ajudar a extrair dados

As informações do pedido também devem incluir quantos de um item está na ordem, como quantas pizzas. Para extrair esses dados, um novo subcomponente aprendido por máquina precisa ser adicionado ao `Order` e esse componente precisa de uma restrição de um número predefinido. Ao restringir a entidade a um número predefinido, a entidade encontrará e extrairá números se o texto for um dígito, `2`ou texto `two`.

Comece adicionando a entidade de número predefinida ao aplicativo. 

1. Selecione **entidades** no menu à esquerda e, em seguida, selecione **+ Adicionar entidade predefinida**. 

1. Na caixa **adicionar entidades predefinidas** , procure e selecione **número** e, em seguida, selecione **concluído**. 

    ![Adicionar entidade pré-criados](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    A entidade predefinida é adicionada ao aplicativo, mas ainda não é uma restrição. 

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Criar entidade de subcomponente com restrição para ajudar a extrair dados

A entidade `Order` deve ter um subcomponente `Quantity` para determinar quantos de um item está na ordem. A quantidade deve ser restrita a um número para que os dados extraídos sejam utilizáveis imediatamente pelo aplicativo cliente. 

Uma restrição é aplicada como uma correspondência de texto, seja com correspondência exata (como uma entidade de lista) ou por meio de expressões regulares (como uma entidade de expressão regular ou uma entidade predefinida). 

Usando uma restrição, somente o texto que corresponde a essa restrição é extraído. 

1. Selecione **entidades** e, em seguida, selecione a entidade `Order`. 
1. Selecione **+ Adicionar componente** e, em seguida, digite o nome `Quantity`, em seguida, selecione Enter para adicionar a nova entidade ao aplicativo.
1. Após a notificação de êxito, selecione o subcomponente `Quantity` e, em seguida, selecione o lápis de restrição.
1. Na lista suspensa, selecione o número predefinido. 

    ![Criar uma entidade de quantidade com um número predefinido como restrição.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    A entidade `Quantity` será aplicada se e somente se o texto correspondente à entidade de número predefinido for encontrado.

    A entidade com a restrição é criada, mas ainda não foi aplicada ao exemplo declarações.

    > [!NOTE]
    > Um subcomponente pode ser aninhado em um subcomponente até 5 níveis. Embora isso não seja mostrado neste artigo, ele está disponível no portal e na API.  

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Exemplo de rótulo expressão para ensinar LUIS sobre a entidade

1. Selecione **tentativas** na navegação à esquerda e, em seguida, selecione a intenção **OrderPizza** . Os três números nos declarações a seguir são rotulados, mas estão visualmente abaixo da linha de entidade `Order`. Esse nível inferior significa que as entidades são encontradas, mas não são consideradas separadas da entidade `Order`.

    ![O número predefinido foi encontrado, mas não é considerado ainda além da entidade Order.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Rotule os números com a entidade `Quantity` selecionando os `2` no exemplo expressão, em seguida, selecionando `Quantity` na lista. Rotule o `6` e o `1` no mesmo exemplo expressão.

    ![Texto do rótulo com entidade de quantidade.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)  

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Treinar o aplicativo para aplicar as alterações de entidade ao aplicativo

Selecione **treinar** para treinar o aplicativo com esses novos declarações.

![Treine o aplicativo e examine o exemplo declarações.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Neste ponto, a ordem tem alguns detalhes que podem ser extraídos (tamanho, quantidade e texto do pedido total). Há ainda mais o refinamento da entidade `Order`, como ingredientes de pizza, tipo de crust e pedidos colaterais. Cada um deles deve ser criado como subcomponentes da entidade `Order`. 

## <a name="test-the-app-to-validate-the-changes"></a>Testar o aplicativo para validar as alterações

Teste o aplicativo usando o painel de **teste** interativo. Esse processo permite que você insira um novo expressão e exiba os resultados da previsão para ver como o aplicativo ativo e treinado está funcionando. A previsão da intenção deve ser razoavelmente confiante (acima de 70%) e a extração de entidade deve pegar pelo menos a entidade `Order`. Os detalhes da entidade do pedido podem estar ausentes porque 5 declarações não são suficientes para lidar com todos os casos.

1. Selecione **Test** (Testar) no painel de navegação superior.
1. Insira o `deliver a medium veggie pizza` expressão e selecione Enter. O modelo ativo prevê a intenção correta com mais de 70% de confiança. 

    ![Insira um novo expressão para testar a intenção.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Selecione **inspecionar** para ver as previsões de entidade.

    ![Exiba as previsões de entidade no painel de teste interativo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    O tamanho foi identificado corretamente. Lembre-se de que o exemplo declarações na `OrderPizza` intenção não tem um exemplo de `medium` como um tamanho, mas usa um descritor de uma `SizeDescriptor` lista de frases que inclui médio.

    A quantidade não foi prevista corretamente. Para corrigir isso, você pode adicionar mais declarações de exemplo usando essa palavra para indicar a quantidade e rotular a palavra como uma entidade `Quantity`. 

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicar o aplicativo para acessá-lo do ponto de extremidade HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction--from-http-endpoint"></a>Obter previsão de intenção e entidade do ponto de extremidade HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL no endereço e insira a mesma consulta que você inseriu no painel de teste interativo. 

    `deliver a medium veggie pizza`

    O último parâmetro querystring é `query`, a expressão **query**. 

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

* [Tutorial – tentativas](luis-quickstart-intents-only.md)
* [Conceito –](luis-concept-entity-types.md) informações conceituais de entidades
* [Conceito-](luis-concept-feature.md) informações conceituais de recursos
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, o aplicativo usa uma entidade aprendida por máquina para descobrir a intenção do expressão de um usuário e extrair detalhes desse expressão. O uso da entidade aprendida por máquina permite decompor os detalhes da entidade.  

> [!div class="nextstepaction"]
> [Adicionar uma entidade keyphrase criada previamente](luis-quickstart-intent-and-key-phrase.md)

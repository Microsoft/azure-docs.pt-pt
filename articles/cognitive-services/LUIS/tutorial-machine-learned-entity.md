---
title: 'Tutorial: extrair dados estruturados com entidade de aprendizagem automática - LUIS'
description: Extrair dados estruturados a partir de uma expressão utilizando a entidade de aprendizagem automática. Para aumentar a precisão da extração, adicione subentidades com características.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: 1f85ba0c64db23e156f384fadcc5ca7bf84a58d4
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130773"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Tutorial: Extrair dados estruturados da expressão do utilizador com entidades de aprendizagem automática em Compreensão linguística (LUIS)

Neste tutorial, extrair dados estruturados de uma expressão utilizando a entidade de aprendizagem automática.

A entidade de machine-learning suporta o [conceito de decomposição](luis-concept-model.md#v3-authoring-model-decomposition) do modelo, fornecendo funcionalidades às entidades sub-entidades. [features](luis-concept-feature.md)

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * App de exemplo de importação
> * Adicionar entidade de aprendizagem automática
> * Adicionar sub-entidade e funcionalidade
> * Trem, Teste, Publicar app
> * Obtenha a previsão da entidade a partir do ponto final

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Porquê usar uma entidade de aprendizagem automática?

Este tutorial adiciona uma entidade de aprendizagem automática para extrair dados da expressão de um utilizador.

A entidade define os dados a extrair de dentro da expressão. Isto inclui dar aos dados um nome, um tipo (se possível), qualquer resolução dos dados em caso de ambiguidade, e o texto exato que compõe os dados.

Para definir os dados, é necessário:
* Criar a entidade
* Rotular o texto, por exemplo, representando a entidade. Estes exemplos rotulados ensinam à LUIS o que é a entidade e onde pode ser encontrada numa expressão.

## <a name="entity-decomposability-is-important"></a>A decomposição da entidade é importante

A decomposição da entidade é importante tanto para a previsão de intenções como para a extração de dados com a entidade.

Comece com uma entidade de aprendizagem automática, que é a entidade de início e de alto nível para a extração de dados. Em seguida, decomponha a entidade em subentidades.

Embora possa não saber o quão detalhado quer a sua entidade quando inicia a sua aplicação, a melhor prática é começar com uma entidade de aprendizagem automática e depois decompor-se com subentidades à medida que a sua app amadurece.

Neste tutorial, você cria uma entidade de aprendizagem automática para representar uma encomenda de uma app de pizza. A entidade extrairá texto relacionado com a ordem, retirando o tamanho e a quantidade.

Uma expressão deve `Please deliver one large cheese pizza to me` extrair `one large cheese pizza` como a ordem, em seguida, também extrair `1` para quantidade e `large` tamanho.

## <a name="download-json-file-for-app"></a>Baixar ficheiro JSON para app

Faça o download e guarde o ficheiro JSON da [aplicação.](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)

## <a name="import-json-file-for-app"></a>Importar ficheiro JSON para aplicação

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Criar entidade aprendida com máquinas

Para extrair detalhes sobre uma encomenda de pizza, crie uma entidade de alto nível, de aprendizagem `Order` automática.

1. Na página **Intenções,** selecione a intenção **OrderPizza.**

1. Na lista de palavras de exemplo, selecione as seguintes expressões.

    |Ordem exemplo expressão|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Clique e arraste o cursor sobre o texto para obter a intenção do primeiro exemplo. no menu que aparece, insira o nome da entidade como `Order` . Em seguida, selecione `Order Create new entity` a partir da lista.

    ![Início e fim do texto para encomenda completa](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Uma entidade não será sempre toda a expressão. Neste caso específico, `pickup` indica como a ordem deve ser recebida. Do ponto de vista conceptual, `pickup` deve fazer parte da entidade rotulada para a encomenda.

1. Na **caixa de tipo de entidade,** selecione **Add Structure** e selecione **Seguinte** . A estrutura é necessária para adicionar subentidades como o tamanho e a quantidade.

    ![Screenshot mostra a janela Escolher uma janela tipo de entidade com a opção de estrutura Add verificada.](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Na caixa **de subentidades adicionar (opcional),** selecione **+** na `Order` linha, adicione e como `Size` `Quantity` subentidades, em seguida, **selecione Criar** .

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra a janela de subentências adicionar (opcional) com subentências realçadas.](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Editar subentidades para melhorar a extração

Os passos anteriores criam a entidade e a sub-entidade. Para melhorar a extração, adicione características às subentígios.

### <a name="improve-size-extraction-with-phrase-list"></a>Melhorar a extração de tamanho com lista de frases

1. Selecione **Entidades** do menu esquerdo e, em seguida, selecione **Entidade de Encomenda.**

1. No **separador Schema e funcionalidades,** selecione a sub-entidade **'Tamanho'** e, em seguida, selecione **+ Adicionar a função** .

1. Selecione Crie uma nova lista de **frases** a partir do menu suspenso.

1. Na nova caixa **de lista de frases,** introduza o nome `SizePhraselist` e introduza valores de: `small` , e `medium` `large` . Quando a caixa **sugestões** preencher, selecione `extra large` e . `xl` . Selecione **Criar** para criar a nova lista de frases.

    Esta funcionalidade de lista de frases ajuda a `Size` sub-entidade a encontrar palavras relacionadas com o tamanho, fornecendo-lhe palavras de exemplo. Esta lista de frases não precisa de incluir todas as palavras de tamanho, mas deve incluir palavras que se espera que indiquem tamanho.

### <a name="add-sizelist-entity"></a>Adicionar entidade SizeList

Adicionar uma lista de tamanhos conhecidos que a aplicação do cliente reconhece também ajudará a extração.

1. Selecione **Entidades** do menu esquerdo e, em seguida, selecione **+ Criar** .

1. Desaver o nome da entidade como `SizeListentity` e desaver o Tipo de **Lista** de modo a que seja fácil de identificar quando comparado com o `SizePhraselist` criado na secção anterior.

1. Adicione os tamanhos que a aplicação do cliente espera: `Small` `Medium` , , `Large` `XLarge` e, em seguida, adicionar sinónimos para cada um. Os sinónimos devem ser os termos que um utilizador introduz no chat bot. A entidade é extraída com uma entidade de lista quando corresponde exatamente ao valor normalizado ou aos sinónimos.

    |Valor normalizado|Sinónimos|
    |--|--|
    |Pequeno|sm, sml, minúsculo, menor|
    |Médio|md, mdm, regular, média, meio|
    |Grande|lg, lrg, grande|
    |XLarge|xl, maior, gigante|


    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra a janela SizeList e os itens da Lista com xLarge selecionados.](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Adicionar funcionalidade da entidade SizeList

1. Selecione **Entidades** do menu esquerdo para voltar à lista de entidades.

1. Selecione **Ordem** da lista de entidades.

1. No **separador Schema e funcionalidades,** selecione a entidade **Size** e, em seguida, selecione **+ Adicionar função** .

1. Selecione **@ SizeListentity** da lista de drop-down.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Adicionar entidade numeram pré-construída

A adição de uma entidade numeram pré-construída também ajudará a extração.

1. Selecione **Entidades** do menu esquerdo e, em seguida, **selecione Adicionar entidade pré-construída.**

1. Selecione **Número** da lista e, em seguida, selecione **Feito** .

1. Selecione **Entidades** do menu esquerdo para voltar à lista de entidades.

### <a name="add-feature-of-prebuilt-number-entity"></a>Adicionar característica da entidade numeram pré-construída

1. Selecione **Ordem** da lista de entidades.

1. No **separador Schema e funcionalidades,** selecione a entidade **Quantity** e, em seguida, selecione **+ Adicionar função** .

1. **Selecione @ número** da lista de drop-down.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Configurar as funcionalidades necessárias

Na página de detalhes da Entidade para entidade **de Encomenda,** selecione o asterisco, `*` para tanto a funcionalidade @ **SizeList** como a função **@ número.** O asterisco aparece na mesma etiqueta que o nome da característica.

> [!div class="mx-imgBorder"]
> ![A screenshot mostra a @SizeList função com o asterisco e aviso de retenção.](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Palavras de exemplo de rótulo

A entidade aprendida com a máquina é criada e as subentidades têm características. Para completar a melhoria da extração, as expressões de exemplo devem ser rotuladas com as subentências.

1. Selecione **Intenções** a partir da navegação à esquerda e, em seguida, selecione a intenção **OrderPizza.**

1. Para abrir a **Paleta de Entidades,** selecionando o **@** símbolo na barra de ferramentas contextual.

1. Selecione cada linha de entidade na paleta e, em seguida, use o cursor de paleta para selecionar a entidade em cada palavra de exemplo. Quando terminar, a lista de entidades deve parecer a seguinte imagem.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parcial da funcionalidade necessária para configurar](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Preparar a aplicação

Para treinar a aplicação, selecione **Train** . A formação aplica as alterações, como as novas entidades e as proclamações rotuladas, ao modelo ativo.

## <a name="add-a-new-example-utterance"></a>Adicione um novo exemplo de expressão

1. Após o treino, adicione um novo exemplo à `OrderPizza` intenção de ver como LUIS entende bem a entidade de aprendizagem automática.

    |Ordem exemplo expressão|
    |--|
    |`I need a large pepperoni pizza`|

    A entidade de topo geral, `Order` é rotulada e a `Size` sub-entidade também é rotulada com linhas pontilhadas.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parcial de novo exemplo de expressão previsto com entidade](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A linha pontilhada indica a previsão com base na aplicação treinada atual.

1. Para alterar a previsão numa entidade rotulada, selecione a marca de verificação na mesma linha.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra uma expressão de exemplo com a marca de verificação realçada.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    Neste momento, a entidade de aprendizagem automática está a trabalhar porque pode encontrar a entidade dentro de um novo exemplo. Ao adicionar declarações de exemplo, se a entidade não for prevista corretamente, rotule a entidade e as subentências. Se a entidade for prevista corretamente, certifique-se de confirmar as previsões.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Treine a app para aplicar as alterações da entidade à app

Selecione **Train** para treinar a app com esta nova expressão.

Neste ponto, a encomenda tem alguns detalhes que podem ser extraídos (tamanho, quantidade e texto de encomenda total). Há ainda refinação da `Order` entidade, como coberturas de pizza, tipo de crosta e ordens laterais. Cada um deles deve ser criado como subentidades da `Order` entidade.

## <a name="test-the-app-to-validate-the-changes"></a>Teste a app para validar as alterações

Teste a aplicação utilizando o painel **de teste** interativo. Este processo permite-lhe introduzir uma nova expressão e depois ver os resultados da previsão para ver até que ponto a aplicação ativa e treinada está a funcionar. A previsão de intenção deve ser bastante confiante (acima de 60%) e a extração da entidade deve recolher pelo menos a `Order` entidade. Os detalhes da entidade da ordem podem estar em falta porque estas poucas expressões não são suficientes para lidar com todos os casos.

1. Selecione **Test** (Testar) no painel de navegação superior.
1. Introduza a expressão `2 small cheese pizzas for pickup` e selecione Enter. O modelo ativo previu a intenção correta com mais de 60% de confiança.


1. **Selecione Inspecionar** para ver as previsões da entidade.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parcial de visualização das previsões da entidade no painel de teste interativo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publique a app para aceder a partir do ponto final HTTP

Para obter uma predição do LUIS num chatbot ou noutra aplicação-cliente, tem de publicar a aplicação no ponto final.

1. Selecione **Publicar** na navegação superior direita.

    ![Screenshot da PUBLICAÇÃO LUIS para o botão de ponta final no menu superior direito](./media/howto-publish/publish-button.png)

1. Selecione a ranhura **de produção** e, em seguida, selecione **Alterar as definições,** selecione **Análise de Sentimento** e, em seguida, selecione **'Fazer'**

    > [!div class="mx-imgBorder"]
    > ![Screenshot da publicação de LUIS para o ponto final](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Selecione o link **URLs do ponto final** no link 'Access' na notificação para aceder à página Recursos **Azure.** Os URLs de ponto final estão listados como **a Consulta exemplo** .

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Obtenha a previsão de intenção e entidade do ponto final http

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até ao final do URL na barra de endereços e substitua _YOUR_QUERY_HERE_ pela mesma consulta que inseriu no painel de teste interativo.

    `2 small cheese pizzas for pickup`

    O último parâmetro de consulta `query` é, a **consulta de** expressão .

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
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
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial - intenções](luis-quickstart-intents-only.md)
* [Conceito - entidades](luis-concept-entity-types.md) informação conceptual
* [Conceito - apresenta](luis-concept-feature.md) informação conceptual
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, a aplicação utiliza uma entidade de aprendizagem automática para encontrar a intenção da expressão de um utilizador e extrair detalhes dessa expressão. A utilização da entidade de aprendizagem automática permite-lhe decompor os detalhes da entidade.

> [!div class="nextstepaction"]
> [Adicionar uma entidade keyphrase criada previamente](luis-quickstart-intent-and-key-phrase.md)

---
title: 'Tutorial: extrair dados estruturados com entidade de machine-learning - LUIS'
description: Extrair dados estruturados de uma expressão utilizando a entidade de aprendizagem automática. Para aumentar a precisão da extração, adicione subentidades com características.
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: f9a732d38d2d4557340080d9d1bbdcf789caea08
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83676167"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Tutorial: Extrair dados estruturados da expressão do utilizador com entidades de machine-learning em Compreensão linguística (LUIS)

Neste tutorial, extrair dados estruturados de uma expressão utilizando a entidade de aprendizagem automática.

A entidade de machine-learning apoia o conceito de [decomposição](luis-concept-model.md#v3-authoring-model-decomposition) do modelo, fornecendo às entidades subentidades [funcionalidades.](luis-concept-feature.md)

**Neste tutorial, vai aprender a:**

> [!div class="checklist"]
> * App de exemplo de importação
> * Adicionar entidade de aprendizagem automática
> * Adicionar subentidade e recurso
> * Treinar, Testar, Publicar app
> * Obtenha a previsão da entidade a partir do ponto final

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Por que usar uma entidade de aprendizagem automática?

Este tutorial adiciona uma entidade de aprendizagem automática para extrair dados da expressão de um utilizador.

A entidade define os dados a extrair de dentro da expressão. Isto inclui dar aos dados um nome, um tipo (se possível), qualquer resolução dos dados se houver ambiguidade, e o texto exato que compõe os dados.

Para definir os dados, é necessário:
* Criar a entidade
* Rotular o texto, dentro de declarações exemplo, representando a entidade. Estes exemplos rotulados ensinam ao LUIS o que é a entidade e onde pode ser encontrada numa expressão.

## <a name="entity-decomposability-is-important"></a>A descomposição da entidade é importante

A descomposição da entidade é importante tanto para a previsão das intenções como para a extração de dados com a entidade.

Comece com uma entidade de aprendizagem automática, que é a entidade de início e de alto nível para a extração de dados. Em seguida, decomponha a entidade em subentidades.

Embora não saiba o quão detalhado pretende a sua entidade quando iniciar a sua aplicação, a melhor prática é começar com uma entidade de aprendizagem automática e depois decompor-se com subentidades à medida que a sua aplicação amadurece.

Neste tutorial, cria-se uma entidade de machine-learning para representar uma encomenda de uma aplicação de pizza. A entidade irá extrair texto relacionado com a ordem, retirando o tamanho e a quantidade.

Uma expressão de `Please deliver one large cheese pizza to me` deve extrair `one large cheese pizza` como ordem, em seguida, extrair também `1` para quantidade e para `large` tamanho.

## <a name="download-json-file-for-app"></a>Descarregue o ficheiro JSON para aplicação

Descarregue e guarde o [ficheiro JSON](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)da aplicação .

## <a name="import-json-file-for-app"></a>Importar ficheiro JSON para app

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Criar entidade aprendida por máquinas

Para extrair detalhes sobre uma encomenda de pizza, crie uma entidade de alto nível, de aprendizagem `Order` automática.

1. Na página **Intenções,** selecione a intenção **OrderPizza.**

1. Na lista de pronunciamentos de exemplo, selecione a seguinte expressão.

    |Pronunciamento de exemplo de ordem|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Comece a selecionar pouco antes do texto mais à esquerda de `pickup` (#1), em seguida, ir além do texto mais à direita , (#2 - isto termina o processo de `anchovies` rotulagem). Aparece um menu pop-up. Na caixa pop-up, insira o nome da entidade como `Order` (#3). Em seguida, selecione `Order Create new entity` a partir da lista (#4).

    ![Etiqueta de início e fim do texto para encomenda completa](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Uma entidade nem sempre será a expressão completa. Neste caso específico, `pickup` indica como a ordem deve ser recebida. Do ponto de vista conceptual, `pickup` deve fazer parte da entidade rotulada para a encomenda.

1. Na caixa de **tipo Desemletra uma entidade,** selecione **Adicionar Estrutura** e, em seguida, selecione **Next**. A estrutura é necessária para adicionar subentidades como o tamanho e a quantidade.

    ![Adicionar estrutura à entidade](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Na caixa **adicionar subentidades (opcional),** selecione **+** na `Order` linha, adicione `Size` `Quantity` e, em seguida, como subentidades, e, em seguida, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar estrutura à entidade](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Editar subentidades para melhorar a extração

Os passos anteriores criam a entidade e a subentidade. Para melhorar a extração, adicione características às subentidades.

### <a name="improve-size-extraction-with-phrase-list"></a>Melhorar a extração de tamanho com lista de frases

1. Selecione **Entidades** do menu esquerdo e, em seguida, selecione a entidade **Encomendar.**

1. No **separador Schema e funcionalidades,** selecione a subentidade **tamanho** e, em seguida, selecione **+ Adicionar funcionalidade**.

1. Selecione Criar uma nova lista de **frases** a partir do menu suspenso.

1. Na nova lista de **frases Criar,** introduza o nome `SizePhraselist` e introduza valores de: `small` , e `medium` `large` . Quando **a** caixa sugestões preencher, selecione `extra large` , e `xl` . Selecione **Guardar** para criar a nova lista de frases.

    Esta funcionalidade de lista de frases ajuda a `Size` subentidade a encontrar palavras relacionadas com o tamanho, fornecendo-lhe palavras exemplo. Esta lista de frases não precisa de incluir todas as palavras de tamanho, mas deve incluir palavras que se espera que indiquem tamanho.

### <a name="add-sizelist-entity"></a>Adicionar entidade SizeList

Adicionar uma lista de tamanhos conhecidos que a aplicação do cliente reconhece também ajudará a extrair.

1. Selecione **Entidades** do menu esquerdo e selecione **+ Criar**.

1. Dete teo nome da entidade como `SizeListentity` sendo fácil de identificar quando comparado com o criado na secção `SizePhraselist` anterior.

1. Adicione os tamanhos que a aplicação do cliente espera: `Small` `Medium` , , `Large` `XLarge` e, em seguida, adicione sinónimos para cada um. Os sinónimos devem ser os termos que um utilizador introduz no chat bot. A entidade é extraída com uma entidade da lista quando corresponde exatamente ao valor normalizado ou sinónimos.

    |Valor normalizado|Sinónimos|
    |--|--|
    |Pequeno|sm, sml, minúsculo, menor|
    |Médio|md, mdm, regular, médio, médio|
    |Grande|lg, lrg, grande|
    |XLarge|xl, maior, gigante|


    > [!div class="mx-imgBorder"]
    > ![Adicionar estrutura à entidade](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Adicionar funcionalidade da entidade SizeList

1. Selecione **Entidades** do menu esquerdo para voltar à lista de entidades.

1. Selecione **Encomenda** da lista de entidades.

1. No **separador Schema e funcionalidades,** selecione a entidade **Tamanho** e, em seguida, selecione **+ Adicionar funcionalidade**.

1. Selecione **@ SizeList** da lista de lançamentos.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Adicionar entidade de número pré-construído

Adicionar uma entidade de números pré-construído também ajudará a extrair.

1. Selecione **Entidades** do menu esquerdo e selecione **Adicionar entidade pré-construída**.

1. Selecione **Número** da lista e selecione **Done**.

1. Selecione **Entidades** do menu esquerdo para voltar à lista de entidades.

### <a name="add-feature-of-prebuilt-number-entity"></a>Adicionar recurso da entidade número pré-construída

1. Selecione **Encomenda** da lista de entidades.

1. No **separador Schema e funcionalidades,** selecione a entidade **quantity** e, em seguida, selecione **+ Adicionar funcionalidade**.

1. Selecione **@ número** da lista de lançamentos.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Configurar as funcionalidades necessárias

Na página de detalhes da Entidade para a entidade **Order,** selecione o asterisco, `*` tanto para a funcionalidade @ **SizeList** como para a funcionalidade **número @.** O asterisco aparece no mesmo rótulo que o nome da funcionalidade.

> [!div class="mx-imgBorder"]
> ![Adicionar estrutura à entidade](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Pronunciamentos de exemplo de etiqueta

A entidade aprendida pela máquina é criada e as subentidades têm características. Para completar a melhoria da extração, as declarações de exemplo devem ser rotuladas com as subentidades.

1. Selecione **Intenções** a partir da navegação esquerda e, em seguida, selecione a intenção **OrderPizza.**

1. Para abrir a Paleta de **Entidades,** selecionando o símbolo na barra de **@** ferramentas contextual.

1. Selecione cada linha de entidade na paleta e, em seguida, use o cursor de paleta para selecionar a entidade em cada expressão de exemplo. Quando terminar, a lista de entidades deve parecer a seguinte imagem.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parcial da configuração necessária](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Preparar a aplicação

Para treinar a aplicação, selecione **Train**. A formação aplica as alterações, como as novas entidades e as expressões rotuladas, ao modelo ativo.

## <a name="add-a-new-example-utterance"></a>Adicione uma nova expressão de exemplo

1. Após o treino, adicione um novo exemplo de expressão à intenção de `OrderPizza` ver como a LUIS compreende bem a entidade de machine-learning.

    |Pronunciamento de exemplo de ordem|
    |--|
    |`I need a large pepperoni pizza`|

    A entidade de topo `Order` global, está rotulada e a `Size` subentidade também está rotulada com linhas pontilhadas.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parcial de nova expressão de exemplo prevista com entidade](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A linha pontilhada indica a previsão com base na aplicação treinada atual.

1. Para alterar a previsão numa entidade rotulada, selecione a marca de verificação na mesma linha.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parcial de nova expressão de exemplo prevista com entidade](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    Neste momento, a entidade de machine-learning está a trabalhar porque pode encontrar a entidade dentro de um novo exemplo. Como acrescenta declarações exemplo, se a entidade não for prevista corretamente, rotule a entidade e as subentidades. Se a entidade estiver corretamente prevista, certifique-se de confirmar as previsões.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Treine a app para aplicar as alterações da entidade na app

Selecione **Train** para treinar a app com esta nova expressão.

Neste ponto, a encomenda tem alguns detalhes que podem ser extraídos (tamanho, quantidade e texto total de encomenda). Há ainda refinação da `Order` entidade, como coberturas de pizza, tipo de crosta e encomendas laterais. Cada uma delas deve ser criada como subentidades da `Order` entidade.

## <a name="test-the-app-to-validate-the-changes"></a>Teste a app para validar as alterações

Teste a aplicação utilizando o painel de **teste** interativo. Este processo permite-lhe entrar numa nova expressão e depois ver os resultados da previsão para ver como a aplicação ativa e treinada está a funcionar. A previsão das intenções deve ser bastante confiante (acima de 60%) e a extração da entidade deve recolher pelo menos a `Order` entidade. Os detalhes da entidade da ordem podem estar desaparecidos porque estas poucas declarações não são suficientes para lidar com todos os casos.

1. Selecione **Test** (Testar) no painel de navegação superior.
1. Introduza a expressão `2 small cheese pizzas for pickup` e selecione Enter. O modelo ativo previu a intenção correta com mais de 60% de confiança.


1. **Selecione Inspecionar** para ver as previsões da entidade.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parcial da visualização das previsões da entidade no painel de teste interativo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publique a app para aceder a ela a partir do ponto final http

Para obter uma predição do LUIS num chatbot ou noutra aplicação-cliente, tem de publicar a aplicação no ponto final.

1. Selecione **Publicar** na navegação de extrema-direita.

    ![Screenshot de LUIS publicar para botão endpoint no menu superior direito](./media/howto-publish/publish-button.png)

1. Selecione a ranhura **de Produção** e, em seguida, selecione **definições de alteração,** selecione **Análise de Sentimento,** e, em seguida, selecione **Done**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da publicação LUIS para endpoint](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Selecione o link **Access your endpoint URLs** na notificação para ir à página **de Recursos Azure.** Os URLs de ponto final estão listados como a Consulta de **Exemplo**.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Obtenha intenção e previsão de entidade do ponto final do HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até ao final do URL na barra de endereços e substitua _YOUR_QUERY_HERE_ pela mesma consulta que inseriu no painel de teste interativo.

    `2 small cheese pizzas for pickup`

    O último parâmetro de corda `query` é, a **consulta**de expressão.

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
* [Conceito - informação](luis-concept-entity-types.md) conceptual de entidades
* [Conceito - apresenta](luis-concept-feature.md) informação conceptual
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Próximos passos

Neste tutorial, a aplicação utiliza uma entidade de machine-learning para encontrar a intenção da expressão de um utilizador e extrair detalhes dessa expressão. A utilização da entidade de machine-learning permite-lhe decompor os detalhes da entidade.

> [!div class="nextstepaction"]
> [Adicionar uma entidade keyphrase criada previamente](luis-quickstart-intent-and-key-phrase.md)

---
title: 'Tutorial: Testes em lote para encontrar problemas - LUIS'
description: Este tutorial demonstra como usar testes de lote para validar a qualidade da sua aplicação Language Understanding (LUIS).
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250487"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutorial: Conjuntos de dados de teste de lote

Este tutorial demonstra como usar testes de lote para validar a qualidade da sua aplicação Language Understanding (LUIS).

Teste de batch permite-lhe validar ativo, formação estado do modelo com um conjunto conhecido de expressões etiquetados com e entidades. No ficheiro batch formatada em JSON, adicionar as expressões e definir as etiquetas de entidade que tem de prever dentro da expressão.

Requisitos do teste de batch:

* Máximo de 1000 discursos por teste.
* Não contém duplicados.
* Tipos de entidades permitidos: apenas entidades aprendidas com máquinas.

Ao utilizar uma aplicação diferente deste tutorial, *não* utilize as declarações de exemplo já adicionadas à sua aplicação.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App de exemplo de importação
> * Crie um ficheiro de teste do batch
> * Executar um teste de batch
> * Rever os resultados de teste

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>App de exemplo de importação

Importar uma app que tome uma encomenda de pizza como `1 pepperoni pizza on thin crust`.

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Utilize o [portal DE pré-visualização LUIS,](https://preview.luis.ai/)importe o JSON numa nova app, nomeie a app `Pizza app`.

1. Selecione **Comboio** no canto superior direito da navegação para treinar a aplicação.

## <a name="what-should-the-batch-file-utterances-include"></a>O que as expressões do ficheiro do lote devem incluir

O ficheiro do lote deve incluir expressões com entidades de alto nível aprendidas com máquinas rotuladas, incluindo a posição de início e fim. As declarações não devem fazer parte dos exemplos já na aplicação. Devem ser declarações que pretende prever positivamente para intenções e entidades.

Pode separar os testes por intenção e/ou entidade ou ter todos os testes (até 1000 expressões) no mesmo ficheiro.

## <a name="batch-file"></a>Arquivo em lotes

O exemplo JSON inclui uma expressão com uma entidade rotulada para ilustrar como é um ficheiro de teste. Nos seus próprios testes, deve ter muitas declarações com a intenção correta e entidade aprendida com máquinas rotuladas.

1. Crie `pizza-with-machine-learned-entity-test.json` num editor de texto ou [descarregue-o.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)

2. No ficheiro de lote formado pela JSON, adicione uma expressão com a **Intenção** que pretende prevista no teste.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>A executar o batch

1. Selecione **Teste** na barra de navegação superior.

2. Selecione **painel de ensaio** de lote no painel do lado direito.

3. Selecione **conjunto de dados de importação**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da app LUIS com conjunto de dados de importação destacado](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Escolha a localização do ficheiro do ficheiro `pizza-with-machine-learned-entity-test.json`.

5. Nomeie o conjunto de dados `pizza test` e selecione **Done**.

    > [!div class="mx-imgBorder"]
    > ![Selecione](./media/luis-tutorial-batch-testing/import-dataset-modal.png) de ficheiros

6. Selecionar o botão **Executar**.

7. Selecione **Ver resultados**.

8. Reveja os resultados no gráfico e da legenda.

## <a name="review-batch-results-for-intents"></a>Rever os resultados do lote para intenções

Os resultados dos testes mostram graficamente como as declarações dos testes foram previstas contra a versão ativa.

O gráfico de batch apresenta quatro quadrantes de resultados. À direita do gráfico é um filtro. O filtro contém intenções e entidades. Quando selecionar uma [secção do gráfico](luis-concept-batch-test.md#batch-test-results) ou um ponto dentro do gráfico, a expressão associada(s) mostra abaixo do gráfico.

Ao passar o rato sobre o gráfico, a roda do rato pode aumentar ou reduzir a apresentar no gráfico. Isto é útil quando há muitos pontos no gráfico rigidamente agrupado.

O gráfico está em quatro quadrantes, com duas das seções apresentadas a vermelho.

1. Selecione a intenção **De ModificarOrder** na lista de filtros.

    > [!div class="mx-imgBorder"]
    > ![Selecione modificar a intenção de ModificarOrder a partir de](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png) da lista de filtros

    A expressão é prevista como um **True Positive,** o que significa que a expressão correspondeu com sucesso à sua previsão positiva listada no ficheiro do lote.

    > [!div class="mx-imgBorder"]
    > ![Utterance com sucesso igualou a sua previsão positiva](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    As marcas de verificação verdes na lista de filtros também indicam o sucesso do teste para cada intenção. Todas as outras intenções são listadas com uma pontuação positiva de 1/1 porque a expressão foi testada contra cada intenção, como um teste negativo para quaisquer intenções não enumeradas no teste do lote.

1. Selecione a intenção de **confirmação.** Esta intenção não está listada no teste do lote, por isso este é um teste negativo da expressão que está listada no teste do lote.

    > [!div class="mx-imgBorder"]
    > ![Utterance previu com sucesso negativo para intenção não listada em ficheiro sinuoso](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    O teste negativo foi bem sucedido, como notado com o texto verde no filtro, e a grelha.

## <a name="review-batch-test-results-for-entities"></a>Rever os resultados dos testes de lote para entidades

A entidade ModificarOrder, como entidade máquina com subentidades, exibe se a entidade de alto nível corresponde e mostra como as subentidades são previstas.

1. Selecione a entidade **ModificarOrder** na lista de filtros e, em seguida, selecione o círculo na grelha.

1. A previsão da entidade mostra abaixo do gráfico. O ecrã inclui linhas sólidas para previsões que correspondem às expectativas e linhas pontilhadas para previsões que não correspondem à expectativa.

    > [!div class="mx-imgBorder"]
    > ![entidade-mãe previu com sucesso em](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png) de ficheiros de lote

## <a name="finding-errors-with-a-batch-test"></a>Encontrar erros com um teste de lote

Este tutorial mostrou-lhe como fazer um teste e interpretar resultados. Não cobriu a filosofia de teste nem como reagir a testes falhados.

* Certifique-se de cobrir as declarações positivas e negativas no seu teste, incluindo as expressões que podem ser previstas para uma intenção diferente, mas relacionada.
* Para falhar as expressões, execute as seguintes tarefas e execute novamente os testes:
    * Reveja os exemplos atuais para intenções e entidades, validar as declarações exemplo da versão ativa são corretas tanto para a intenção como para a rotulagem de entidades.
    * Adicione funcionalidades que ajudam a sua app a prever intenções e entidades
    * Adicione mais declarações positivas de exemplo
    * Rever o balanço das declarações de exemplo através das intenções

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Passo seguinte

O tutorial usou um teste de lote para validar o modelo atual.

> [!div class="nextstepaction"]
> [Saiba sobre padrões](luis-tutorial-pattern.md)


---
title: 'Tutorial: Testes em lote para encontrar problemas - LUIS'
description: Este tutorial demonstra como usar testes de lote para validar a qualidade da sua aplicação Language Understanding (LUIS).
ms.topic: tutorial
ms.date: 03/02/2020
ms.openlocfilehash: c276f0b52f83937fbe3b6fd9e0b7c1a66f665095
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78250487"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutorial: Conjuntos de dados de teste de lote

Este tutorial demonstra como usar testes de lote para validar a qualidade da sua aplicação Language Understanding (LUIS).

O teste do lote permite-lhe validar o estado do modelo ativo e treinado com um conjunto conhecido de expressões e entidades etiquetadas. No ficheiro de lote formado pela JSON, adicione as palavras e coloque as etiquetas de entidade que precisa previstas dentro da expressão.

Requisitos para o teste do lote:

* Máximo de 1000 expressões por teste.
* Sem duplicados.
* Tipos de entidades permitidos: apenas entidades aprendidas com máquinas.

Ao utilizar uma aplicação diferente deste tutorial, *não* utilize as declarações de exemplo já adicionadas à sua aplicação.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App de exemplo de importação
> * Criar um ficheiro de teste de lote
> * Realizar um teste de lote
> * Analisar os resultados dos testes

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>App de exemplo de importação

Importar uma app que requer `1 pepperoni pizza on thin crust`uma encomenda de pizza como .

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Utilize o [portal DE pré-visualização LUIS,](https://preview.luis.ai/)importe `Pizza app`o JSON numa nova aplicação, nomeie a app .

1. Selecione **Comboio** no canto superior direito da navegação para treinar a aplicação.

## <a name="what-should-the-batch-file-utterances-include"></a>O que as expressões do ficheiro do lote devem incluir

O ficheiro do lote deve incluir expressões com entidades de alto nível aprendidas com máquinas rotuladas, incluindo a posição de início e fim. As declarações não devem fazer parte dos exemplos já na aplicação. Devem ser declarações que pretende prever positivamente para intenções e entidades.

Pode separar os testes por intenção e/ou entidade ou ter todos os testes (até 1000 expressões) no mesmo ficheiro.

## <a name="batch-file"></a>Ficheiro de lote

O exemplo JSON inclui uma expressão com uma entidade rotulada para ilustrar como é um ficheiro de teste. Nos seus próprios testes, deve ter muitas declarações com a intenção correta e entidade aprendida com máquinas rotuladas.

1. Crie `pizza-with-machine-learned-entity-test.json` num editor de texto ou [descarregue-o.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)

2. No ficheiro de lote formado pela JSON, adicione uma expressão com a **Intenção** que pretende prevista no teste.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Executar o lote

1. Selecione **Teste** na barra de navegação superior.

2. Selecione **painel de ensaio** de lote no painel do lado direito.

3. Selecione **conjunto de dados de importação**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da app LUIS com conjunto de dados de importação em destaque](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Escolha a localização `pizza-with-machine-learned-entity-test.json` do ficheiro do ficheiro.

5. Nomeie `pizza test` o conjunto de dados e selecione **Done**.

    > [!div class="mx-imgBorder"]
    > ![Selecione ficheiro](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Selecionar o botão **Executar**.

7. Selecione **Ver resultados**.

8. A revisão resulta no gráfico e na lenda.

## <a name="review-batch-results-for-intents"></a>Rever os resultados do lote para intenções

Os resultados dos testes mostram graficamente como as declarações dos testes foram previstas contra a versão ativa.

O gráfico do lote apresenta quatro quadrantes de resultados. À direita da tabela está um filtro. O filtro contém intenções e entidades. Quando selecionar uma [secção do gráfico](luis-concept-batch-test.md#batch-test-results) ou um ponto dentro do gráfico, a expressão associada(s) mostra abaixo do gráfico.

Enquanto paira sobre a tabela, uma roda de rato pode ampliar ou reduzir o visor na tabela. Isto é útil quando há muitos pontos na tabela agrupados firmemente.

O gráfico está em quatro quadrantes, com duas das secções expostas a vermelho.

1. Selecione a intenção **De ModificarOrder** na lista de filtros.

    > [!div class="mx-imgBorder"]
    > ![Selecione modificar a intenção de Modificar Order da lista de filtros](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    A expressão é prevista como um **True Positive,** o que significa que a expressão correspondeu com sucesso à sua previsão positiva listada no ficheiro do lote.

    > [!div class="mx-imgBorder"]
    > ![A expressão correspondeu com sucesso à sua previsão positiva](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    As marcas de verificação verdes na lista de filtros também indicam o sucesso do teste para cada intenção. Todas as outras intenções são listadas com uma pontuação positiva de 1/1 porque a expressão foi testada contra cada intenção, como um teste negativo para quaisquer intenções não enumeradas no teste do lote.

1. Selecione a intenção de **confirmação.** Esta intenção não está listada no teste do lote, por isso este é um teste negativo da expressão que está listada no teste do lote.

    > [!div class="mx-imgBorder"]
    > ![Utterance previu negativamente com sucesso para intenção não listada em ficheiro de lote](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    O teste negativo foi bem sucedido, como notado com o texto verde no filtro, e a grelha.

## <a name="review-batch-test-results-for-entities"></a>Rever os resultados dos testes de lote para entidades

A entidade ModificarOrder, como entidade máquina com subentidades, exibe se a entidade de alto nível corresponde e mostra como as subentidades são previstas.

1. Selecione a entidade **ModificarOrder** na lista de filtros e, em seguida, selecione o círculo na grelha.

1. A previsão da entidade mostra abaixo do gráfico. O ecrã inclui linhas sólidas para previsões que correspondem às expectativas e linhas pontilhadas para previsões que não correspondem à expectativa.

    > [!div class="mx-imgBorder"]
    > ![Entidade-mãe prevista com sucesso em ficheiro de lote](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

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


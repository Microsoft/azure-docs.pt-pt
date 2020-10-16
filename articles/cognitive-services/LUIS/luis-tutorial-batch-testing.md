---
title: 'Tutorial: Teste de lote para encontrar problemas - LUIS'
description: Este tutorial demonstra como usar testes de lote para validar a qualidade da sua aplicação de Compreensão linguística (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: 7dd181f8cd398dd683296b446028b398a9800b53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298324"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutorial: Conjuntos de dados de teste de lote

Este tutorial demonstra como usar testes de lote para validar a qualidade da sua aplicação de Compreensão linguística (LUIS).

O teste de lote permite validar o estado do modelo ativo e treinado com um conjunto conhecido de expressões e entidades rotuladas. No ficheiro de lote com formato JSON, adicione as expressões e desave as etiquetas da entidade que precisa previstas dentro da expressão.

Requisitos para o ensaio do lote:

* Máximo de 1000 proclamações por teste.
* Sem duplicados.
* Tipos de entidades permitidos: apenas entidades com aprendizagem automática.

Quando utilizar uma aplicação diferente deste tutorial, *não* utilize as palavras de exemplo já adicionadas à sua app.

**Neste tutorial, ficará a saber como:**

<!-- green checkmark -->
> [!div class="checklist"]
> * App de exemplo de importação
> * Criar um ficheiro de teste de lote
> * Fazer um teste de lote
> * Rever os resultados dos testes

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>App de exemplo de importação

Importe uma aplicação que leve uma encomenda de pizza como `1 pepperoni pizza on thin crust` .

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Importe o JSON numa nova aplicação, nomeie a `Pizza app` app.


1. Selecione **Train** no canto superior direito da navegação para treinar a aplicação.

## <a name="what-should-the-batch-file-utterances-include"></a>O que devem as expressões de ficheiros de lote incluem

O ficheiro do lote deve incluir expressões com entidades de aprendizagem automática de alto nível rotuladas, incluindo posição inicial e final. As declarações não devem fazer parte dos exemplos já na app. Devem ser expressões que se pretende prever positivamente para as intenções e entidades.

Pode separar os testes por intenção e/ou entidade ou ter todos os testes (até 1000 expressões) no mesmo ficheiro.

## <a name="batch-file"></a>Arquivo de lote

O exemplo JSON inclui uma expressão com uma entidade rotulada para ilustrar como é um ficheiro de teste. Nos seus próprios testes, deve ter muitas expressões com a intenção correta e a entidade de aprendizagem automática rotulada.

1. Crie `pizza-with-machine-learned-entity-test.json` num editor de texto ou [descarregue-o.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)

2. No ficheiro de lote em formatado JSON, adicione uma expressão com a **Intenção** que pretende prever no teste.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Executar o lote

1. Selecione **Teste** na barra de navegação superior.

2. Selecione painel de **testes** de lote no painel do lado direito.

3. Selecione **Conjunto de dados de importação**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da app LUIS com conjunto de dados de importação em destaque](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. Escolha a localização do ficheiro `pizza-with-machine-learned-entity-test.json` do ficheiro.

5. Nomeie o conjunto de dados `pizza test` e selecione **Feito**.

    > [!div class="mx-imgBorder"]
    > ![Selecione ficheiro](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. Selecione o botão **Run** (Executar).

7. Selecione **Ver resultados**.

8. Rever os resultados no gráfico e na lenda.

## <a name="review-batch-results-for-intents"></a>Rever os resultados do lote para intenções

Os resultados dos testes mostram graficamente como as declarações de teste foram previstas contra a versão ativa.

O gráfico do lote apresenta quatro quadrantes de resultados. À direita da tabela está um filtro. O filtro contém intenções e entidades. Quando seleciona uma [secção do gráfico](luis-concept-batch-test.md#batch-test-results) ou um ponto dentro da tabela, o(s) visualização de expressão associada abaixo da tabela.

Enquanto paira sobre a tabela, uma roda de rato pode ampliar ou reduzir o visor na tabela. Isto é útil quando há muitos pontos na tabela agrupados firmemente juntos.

O gráfico está em quatro quadrantes, com duas das secções exibidas a vermelho.

1. Selecione a intenção **modificar-se** na lista de filtros.

    > [!div class="mx-imgBorder"]
    > ![Selecione Modificar Intenção de Modificar da lista de filtros](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    A expressão é prevista como um **Verdadeiro Positivo,** o que significa que a expressão correspondeu com sucesso à sua previsão positiva listada no ficheiro do lote.

    > [!div class="mx-imgBorder"]
    > ![A expressão correspondeu com sucesso à sua previsão positiva](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    As marcas verdes na lista de filtros também indicam o sucesso do teste para cada intenção. Todas as outras intenções são listadas com uma pontuação positiva de 1/1 porque a expressão foi testada contra cada intenção, como um teste negativo para quaisquer intenções não listadas no teste do lote.

1. Selecione a intenção **de confirmação.** Esta intenção não está listada no teste do lote, pelo que este é um teste negativo da expressão que está listada no teste do lote.

    > [!div class="mx-imgBorder"]
    > ![Pronunciação com sucesso previu negativo para intenção não cotada no ficheiro de lote](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    O teste negativo foi bem sucedido, como se nota com o texto verde no filtro e na grelha.

## <a name="review-batch-test-results-for-entities"></a>Rever os resultados dos testes de lote para entidades

A entidade ModificarOrder, enquanto entidade de máquina com subentidades, exibe se a entidade de nível superior corresponde e mostrar como as subentígios são previstas.

1. Selecione a entidade **ModificarOrder** na lista de filtros e, em seguida, selecione o círculo na grelha.

1. A previsão da entidade apresenta abaixo do gráfico. O ecrã inclui linhas sólidas para previsões que correspondem às expectativas e linhas pontilhadas para previsões que não correspondem às expectativas.

    > [!div class="mx-imgBorder"]
    > ![Progenitor da entidade com sucesso previsto em ficheiro de lote](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>Encontrar erros com um teste de lote

Este tutorial mostrou-lhe como fazer um teste e interpretar resultados. Não cobria a filosofia de teste ou como responder a testes falhados.

* Certifique-se de cobrir expressões positivas e negativas no seu teste, incluindo expressões que podem ser previstas para uma intenção diferente, mas relacionada.
* Para não obter declarações falhadas, execute as seguintes tarefas e, em seguida, execute novamente os testes:
    * Reveja os exemplos atuais para intenções e entidades, valide as declarações de exemplo da versão ativa são corretas tanto para a intenção como para a rotulagem de entidades.
    * Adicione funcionalidades que ajudam a sua app a prever intenções e entidades
    * Adicione mais palavras de exemplo positivas
    * Rever o equilíbrio das expressões de exemplo através de intenções

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>Passo seguinte

O tutorial usou um teste de lote para validar o modelo atual.

> [!div class="nextstepaction"]
> [Saiba mais sobre padrões](luis-tutorial-pattern.md)


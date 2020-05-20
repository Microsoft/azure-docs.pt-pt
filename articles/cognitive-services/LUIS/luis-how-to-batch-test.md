---
title: Como realizar um teste de lote - LUIS
titleSuffix: Azure Cognitive Services
description: Utilize conjuntos de testes de lote de compreensão linguística (LUIS) para encontrar expressões com intençãos e entidades incorretas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 4d3535d419b0f99491795b3920fdd295e3128299
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654012"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Teste de lote com um conjunto de pronunciações de exemplo

 O teste do lote é um teste abrangente no seu modelo treinado atual para medir o seu desempenho em LUIS. Os conjuntos de dados utilizados para o teste do lote não devem incluir declarações exemplo sintetizações nas intenções ou expressões recebidas do ponto final do prazo de previsão.

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>Importar um ficheiro conjunto de dados para testes de lote

1. Selecione **Teste** na barra superior e, em seguida, selecione **painel de teste batch**.

    ![Ligação de teste de lote](./media/luis-how-to-batch-test/batch-testing-link.png)

2. Selecione **conjunto de dados de importação**. Aparece a nova caixa de diálogo de conjunto de **dados Import.** Selecione **Escolha o Ficheiro** e localize um ficheiro JSON com o [formato JSON](luis-concept-batch-test.md#batch-file-format) correto que não contém *mais de 1.000* expressões para testar.

    Erros de importação são relatados numa barra de notificação vermelha no topo do navegador. Quando uma importação tem erros, não é criado nenhum conjunto de dados. Para mais informações, consulte [erros comuns](luis-concept-batch-test.md#common-errors-importing-a-batch).

3. No campo **Dataset Name,** introduza um nome para o seu ficheiro dataset. O ficheiro dataset inclui um **conjunto de expressões,** incluindo a intenção e *entidades* *etiquetadas.* Reveja o ficheiro de [lote de exemplo](luis-concept-batch-test.md#batch-file-format) para sintaxe.

4. Selecione **Done** (Concluído). O ficheiro do conjunto de dados é adicionado.

## <a name="run-rename-export-or-delete-dataset"></a>Executar, mudar nome, exportar ou eliminar conjunto de dados

Para executar, mudar o nome, exportar ou eliminar o conjunto de dados, utilize o botão elipse ***(...***) no final da linha dataset.

> [!div class="mx-imgBorder"]
> ![Screenshot da lista de testes de lote com opções](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>Execute um teste de lote na sua app treinada

Para executar o teste, selecione o nome do conjunto de dados e, em seguida, selecione **Executar** a partir da barra de ferramentas contextual. Quando o teste estiver concluído, esta linha apresenta o resultado do teste do conjunto de dados.

O conjunto de dados descarregado é o mesmo ficheiro que foi enviado para testes de lote.

|Estado|Significado|
|--|--|
|![Ícone de círculo verde de teste bem-sucedido](./media/luis-how-to-batch-test/batch-test-result-green.png)|Todas as declarações são bem sucedidas.|
|![Falha no ícone vermelho x do teste](./media/luis-how-to-batch-test/batch-test-result-red.png)|Pelo menos uma intenção de expressão não correspondeu à previsão.|
|![Pronto para testar ícone](./media/luis-how-to-batch-test/batch-test-result-blue.png)|O teste está pronto para correr.|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>Ver resultados dos testes do lote

Para rever os resultados dos testes do lote, selecione **Ver resultados**.

<a name="filter-chart-results-by-intent-or-entity"></a>

## <a name="filter-chart-results"></a>Resultados da tabela de filtros

Para filtrar o gráfico por uma intenção ou entidade específica, selecione a intenção ou entidade no painel de filtragem do lado direito. Os pontos de dados e a sua atualização de distribuição no gráfico de acordo com a sua seleção.

![Resultado do teste do lote visualizado](./media/luis-how-to-batch-test/filter-by-entity.png)

## <a name="view-single-point-utterance-data"></a>Ver dados de expressão de um ponto único

Na tabela, paire sobre um ponto de dados para ver a pontuação certeza da sua previsão. Selecione um ponto de dados para recuperar a sua expressão correspondente na lista de palavras na parte inferior da página.

![Expressão selecionada](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>Ver dados da secção

No gráfico de quatro secções, selecione o nome da secção, como **O Falso Positivo** no topo direito da tabela. Abaixo do gráfico, todas as expressões nessa secção exibem abaixo do gráfico numa lista.

![Pronunciações selecionadas por secção](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Nesta imagem anterior, a expressão `switch on` é rotulada com a intenção TurnAllOn, mas recebeu a previsão de nenhuma intenção. Esta é uma indicação de que a intenção TurnAllOn precisa de mais declarações de exemplo para fazer a previsão esperada.

As duas secções da tabela em vermelho indicam expressões que não correspondem à previsão esperada. Estes indicam declarações que o LUIS precisa de mais formação.

As duas secções da tabela em verde correspondem à previsão esperada.

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>Passos seguintes

Se os testes indicarem que a sua aplicação LUIS não reconhece as intenções e entidades corretas, pode trabalhar para melhorar o desempenho da sua app LUIS, rotulando mais expressões ou adicionando funcionalidades.

* [Rótulo sugerido expressões com LUIS](luis-how-to-review-endpoint-utterances.md)
* [Utilize funcionalidades para melhorar o desempenho da sua app LUIS](luis-how-to-add-features.md)
* [Compreenda os testes de lote com este tutorial](luis-tutorial-batch-testing.md)
* [Aprenda conceitos](luis-concept-batch-test.md)de teste de lote.
